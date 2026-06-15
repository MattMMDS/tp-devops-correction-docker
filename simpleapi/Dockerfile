# Premiere version
# FROM eclipse-temurin:21-jre-alpine

# WORKDIR /app

# COPY Main.class .

# CMD ["java", "Main"]

# Second multistage
# # Étape de build
# FROM eclipse-temurin:21-jdk-alpine AS builder

# WORKDIR /app

# COPY Main.java .

# RUN javac Main.java

# # Étape d'exécution
# FROM eclipse-temurin:21-jre-alpine

# WORKDIR /app

# COPY --from=builder /app/Main.class .

# CMD ["java", "Main"]

# mvn version
# Build stage
FROM eclipse-temurin:21-jdk-alpine AS build
WORKDIR /app
RUN apk add --no-cache maven
# Copie du pom.xml en premier pour profiter du cache Docker
COPY ./pom.xml .
# Télécharge les dépendances
RUN mvn dependency:go-offline
# Copie du code source
COPY ./src ./src
# Build du jar
RUN mvn clean package -DskipTests

# Run stage
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]

# Build stage
FROM eclipse-temurin:21-jdk-alpine AS myapp-build
ENV MYAPP_HOME=/opt/myapp
WORKDIR $MYAPP_HOME
RUN apk add --no-cache maven
COPY ./pom.xml .
COPY ./src ./src
RUN mvn package -DskipTests

# Run stage
FROM eclipse-temurin:21-jre-alpine
ENV MYAPP_HOME=/opt/myapp
WORKDIR $MYAPP_HOME
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar
ENTRYPOINT ["java", "-jar", "myapp.jar"]
