# limage doit etre basée sur une image officielle de node.js version 20 alpine
FROM node:20-alpine

# définit le répertoire de travail dans le conteneur
WORKDIR /app

# copier en premier les fichiers de deinition des dependances
COPY package*.json ./

# installer les dépendances de lapp 
RUN npm install

#copie le rest du code source
COPY . .

# configurer le port
EXPOSE 80

# important, commande de démarrage
CMD ["npm", "start"]