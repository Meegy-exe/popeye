# Projet Popeye
Essai et découverte &amp; Docker.
**Etudiante :** Alison Dehaies - Promo 2027


------------------------------------------------
          INSTALLATION SOUS LINUX (ubuntu)
------------------------------------------------

## Pré-requis
Pour faire tourner ce projet, vous avez besoin de :
1. **Docker** (node.js sera installé automatiquement)
1. **Un compte github** (et une clé SSH configurée)
2. **Un serveur local PHP** (via le terminal).
3. **Git** (pour cloner le projet)

## Note avant de commencer :
Ne pas oublier de créer un gitignore. (mdp dans .env, )


1. Installation Docker
### Vérifier la version d'installation Docker
docker --version

### Mettre à jour et installer les bases
sudo apt-get update
sudo apt-get install ca-certificates curl

### Créer le dossier pour la clé de sécurité
sudo install -m 0755 -d /etc/apt/keyrings

### Télécharger la clé Docker
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

### Ajouter le dépôt Docker à la liste de sources
`echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`

### Installer et mettre à jour
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

Puis redémarrer la session pour prendre en compte les changements.

## PRATIQUE
### Raccourcis docker
sudo usermod -aG docker $USER

## AU CAS OU :
### Nettoyer des mauvaises installations Docker :
sudo apt-get remove docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc

---------------------------------------------------------
                    IMAGES DOCKER
---------------------------------------------------------
## Poll
L'image doit être basée sur une image en *python*.

### Créer un fichier Dockerfile
`FROM python:3.9-slim

WORKDIR /app

COPY . /app

RUN pip3 install -r requirements.txt

EXPOSE 80

CMD ["flask", "run", "--host=0.0.0.0", "--port=80"]`

## Result
L'image doit être basée sur une image officielle Node.js version 20 Alpine.

### Créer un fichier .dockerignore
node_modules

### Créer un fichier Dockerfile
`FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install


COPY . .

EXPOSE 80

CMD ["npm", "start"]` 


## Worker
L'image sera construite à l'aide d'un build multi-étapes.

### Créer un fichier Dockerfile

`FROM maven:3.9.6-eclipse-temurin-21-alpine AS builder

WORKDIR /app

COPY pom.xml .

RUN mvn dependency:resolve

COPY src ./src

RUN mvn package

FROM eclipse-temurin:21-jre-alpine

WORKDIR /app

COPY --from=builder /app/target/worker-jar-with-dependencies.jar .

CMD ["java", "-jar", "worker-jar-with-dependencies.jar"]`


-----------------------------------------------------------------
                      DOCKER COMPOSE
-----------------------------------------------------------------

### Créer un fichier Docker compose
Créez docker-commpose.yml à la racine du projet

IMPORTANT /!\ ne surtout pas écrire en dur les mdp.


### Exécuter compose
docker compose up --build


### Choisir une image 
https://hub.docker.com/

### Pull des images
(s'il y a un message d'erreur, créez vous un compte et connectez vous)
docker pull nginx

### Lister les images
docker images


### Lancer un conteneur
(le conteneur fonctionnera en arrière-plan)
docker run -d nginx
(cela devrait afficher un identifiant)

Pour voir le conteneur en fond :
docker ps

### Arrêter le conteneur
docker stop numéro-d-identifiant

### Supprimer un conteneur
docker rm numéro-d-identifiant

### Run une version spécifique
docker run -d nginx:1.18

### Run sous un port précis
docker run -d -p 8080:80 nginx

### Accéder au site
Ne confondez pas le port du container avec le port de l'host.
Le premier correspond au port sur lequel l'application écoute à l'intérieur du conteneur, et le second est le port sur lequel l'application est accessible depuis l'hôte (et depuis l'extérieur).

http://localhost:8080


### Chercher l'ID du conteneur
(L'ID change à chaque run)
docker ps

### Afficher la date du conteneur en cours d'execution
docker exec <ID> date

### Afficher les logs
docker logs <ID>

### Afficher les logs en temps réél
docker logs -f <ID>


-----------------------------------------------
              FAIRE SA PROPRE IMAGE
-----------------------------------------------

### Configurer Dockerfile :
FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y nodejs npm

COPY . .

RUN npm install

EXPOSE 3000

CMD ["node", "app.js"]

### Exécuter Dockerfile
(prendra environ 20 minutes)
docker build -t <nom-de-votre-choix> .

### Lancer le conteneur
docker run -p <votre-port> <nom-de-votre-choix>

### Accéder au site :
http://localhost:8080

## Docker compose
### Créer un fichier 
docker-compose.yml

### Service jigglypuff-server (voir en bas pour documentation)
- Doit construire l'image en utilisant le Dockerfile
- Doit être au port 8080:3000.
- Définit une variable d'environnement JIGGLYPUUF à shiny (oui le pokémon)

Editer le fichier Docker compose :
services:
  jigglypuff-serveur:
    build: .
    ports:
      - "8080:3000"
    environment:
      - JIGGLYPUFF=shiny

  nginx:
    image: nginx:1.24-alpine
    ports:
    - "5000:80"

### Lancer la commande
docker compose up

### Accèder aux sites
JIGGLYPUFF : http://localhost:8080
NGINX : http://localhost:5000

-----------------------------------------------
              DOCS UTILES
-----------------------------------------------

NOTES & COURS :
https://www.canva.com/design/DAHA6QA6Ndw/uI5r3ZQT5BpKZ6kkscRECA/edit?utm_content=DAHA6QA6Ndw&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton

INSTALL :
https://doc.ubuntu-fr.org/docker


COMMANDES DOCKERS :
https://blog.stephane-robert.info/docs/conteneurs/moteurs-conteneurs/docker/cli/
https://docs.docker.com/reference/cli/docker/

DOCKERS COMPOSE :
https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/docker-compose/