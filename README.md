# TP-DevOps

## 1-1
Dockerfile essentials

Contenu du Docker file :
```
FROM postgres:14.1-alpine

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

## 3-1

Voici le fichier setup.yml dans l'inventory
```
all:
 vars:
   ansible_user: centos
   ansible_ssh_private_key_file: "/home/nickchae/id_rsa"
 children:
   prod:
     hosts: nicolas.coulomb.takima.cloud
```

Le fichier setup.yml est un fichier d'inventaire Ansible qui définit des variables et des groupes de serveurs.

#### Commandes :
```ansible all -i inventories/setup.yml -m ping``` : Vérifier la connectivité aux serveurs répertoriés dans l'inventaire en utilisant le module "ping" d'Ansible.

```ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"``` : Collecter des informations sur les serveurs, notamment la distribution du système d'exploitation, en utilisant le module "setup" d'Ansible.

```ansible all -i inventories/setup.yml -m yum -a "name=httpd state=absent"``` --become : Supprimer le serveur Apache httpd des serveurs en utilisant le module "yum" d'Ansible.

## 3-2

Le playbook comprend des tâches pour installer les dépendances, ajouter le référentiel Docker, installer Docker et garantir que Docker est en cours d'exécution.
Pour exécuter le playbook d'installation Docker, utilisez la commande : ```ansible-playbook -i inventories/setup.yml docker-install.yml```. Le playbook installe Docker sur le serveur.

Pour une meilleure approche, les tâches d'installation Docker sont organisées en un rôle. Le rôle est créé avec ```ansible-galaxy init``` 
Ici on met l'installation dans le role install-docker
Le playbook appelle le rôle docker ainsi que les rôles dédiés au backend, à la db et au proxy comme cela :

```
- name: "Deploy app"
  hosts: all
  gather_facts: false
  become: true
  roles :
    - install-docker
    - create-network
    - launch-database
    - launch-app
    - launch-proxy
```

## Déploiement de votre Application avec Ansible

Configuration des tâches docker_container :

#### Installer Docker :
Utilise le rôle 'install-docker' pour installer Docker sur le serveur.

#### Créer un Réseau :
Utilise le rôle 'create-network' pour créer un réseau Docker.

#### Lancer la Base de Données :
Utilise le rôle 'launch-database' pour démarrer un conteneur Docker pour la base de données.

#### Lancer l'Application :
Utilise le rôle 'launch-app' pour démarrer un conteneur Docker pour le backend.

#### Lancer le Proxy :
Utilise le rôle 'launch-proxy' pour démarrer un conteneur Docker pour le proxy http.