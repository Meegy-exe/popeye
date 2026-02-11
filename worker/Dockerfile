# Etape de compilation

# La première étape doit être basée sur maven:3.9.6-eclipse-temurin-21-alpine et être nomméebuilder.
FROM maven:3.9.6-eclipse-temurin-21-alpine AS builder

# définir le repetoire de travail
WORKDIR /app

# copier pom.xml et resoudre les dependances
COPY pom.xml .

RUN mvn dependency:resolve

# copier le dossier src et compiler le projet
COPY src ./src

RUN mvn package

# etape d'exécution
FROM eclipse-temurin:21-jre-alpine

# definir le repertoire de travail
WORKDIR /app

# recupere le fichier compilé le .jar depuis la premiere étape
# le nom du jar correspond au module worker 
COPY --from=builder /app/target/worker-jar-with-dependencies.jar .


# important, commande de démarrage
CMD ["java", "-jar", "worker-jar-with-dependencies.jar"]