# TP-DevOps

## 1-1
Dockerfile essentials

Contenu du Docker file :
FROM postgres:14.1-alpine
```
ENV POSTGRES_DB=db \
    POSTGRES_USER=usr \
    POSTGRES_PASSWORD=pwd
```
Commandes :

```docker network create```
Créer un réseau pour la communication entre les conteneurs

```docker build```
 build l'image Docker

```docker run```
exécuter le docker

```
docker run \
    -p "8090:8080" \
    --net=app-network \
    --name=adminer \
    -d \
    adminer
```
administrer la base de donnée depuis une interface web


## 1-2

Le multistage permet d'effectuer plusieurs étapes les unes après les autres dans le même conteneur, ici on compile puis on run l'application directement depuis le conteneur au lieu de le faire à la main

## 1-3

La commande ```docker-compose``` permet de lancer, arrêter, et build tous les conteneurs automatiquement
Dans le fichier docker-compose.yml, on renseigne pour chaque partie le nom, l'emplacement du dossier correspondant, ainsi que le réseau créé pour la communication

## 1-5

J'ai push les 3 parties database, backend et httpd avec les commandes :

```
docker tag tp1-database nickchae/tp1-database:1.0
docker push nickchae/tp1-database:1.0

docker tag tp1-backend nickchae/tp1-backend:1.0
docker push nickchae/tp1-backend:1.0

docker tag tp1-httpd nickchae/tp1-httpd:1.0
docker push nickchae/tp1-httpd:1.0

```
## 2-1


Testcontainers est une bibliothèque Java pour créer des conteneurs Docker légers afin de faciliter les tests d'intégration, notamment pour les bases de données.

## 2 - 2

```
- name: Checkout code
        uses: actions/checkout@v2.5.0

      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: 17
          distribution: 'adopt'

      - name: Build and test with Maven
        run: |
          cd /home/runner/work/TP-DevOps/TP-DevOps/backend
          mvn clean verify

```
pour lancer le build et les tests avec Maven on se place dans le bon dossier puis on lance la commande précédemment vue


## 2-3
après avoir désactivé l'analyse automatique de SonarCloud, et adapté cette ligne à mon projet

```
mvn -B verify sonar:sonar -Dsonar.projectKey=SuperRandomGuy_TP-DevOps -Dsonar.organization=superrandomguy -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=${{ secrets.SONAR_TOKEN }}  --file ./backend/pom.xml -e

```
Le workflow fonctionne et le quality gate de SonarCloud passe avec succès

