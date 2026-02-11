# limage doit etre basée sur une image officielle de python
FROM python:3.9-slim

# définit le répertoire de travail dans le conteneur
WORKDIR /app

# copier le code source et les requirements dans le conteneur
COPY . /app

# installer les dépendances de lapp 
RUN pip3 install -r requirements.txt

# configurer le port
EXPOSE 80

# important, commande de démarrage
CMD ["flask", "run", "--host=0.0.0.0", "--port=80"]