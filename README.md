# Projet Popeye
Essai et découverte &amp; Docker.
**Etudiante :** Alison Dehaies - Promo 2027


------------------------------------------------
          INSTALLATION SOUS LINUX (ubuntu)
------------------------------------------------

## Pré-requis
Pour faire tourner ce projet, vous avez besoin de :
1. **Docker** (node.js sera installé automatiquement)
2. **Un compte github** (et une clé SSH configurée)
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
Pour chaque service vous aurez une structure ressemblant à celle ci (ex pour pull):
`
  poll:
    build: ./poll

    ports : 
     - "5000:80"

     - poll-tier

    environment:
     - REDIS_HOST=redis

    depends_on:
     - redis
    restart: always
`


 **IMPORTANT /!\ déclarer *impérativement* dans environments pour PORT, DB, USER et PASSWORD en variables dans un .env .**


### Exécuter compose
docker compose up --build


### Accèder aux sites
Favorite DevOps : http://localhost:5000
Résulats des votes : http://localhost:5001


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