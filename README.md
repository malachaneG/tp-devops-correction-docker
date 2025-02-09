2.1 Les test containers sont une librairie java quipermet d'utiliser des conteneur docker pour faire des tests des différents composant dans un environnement réaliste.
Par exemple un conteneur postgres pour tester une bdd postgres

création du main.yml
commit et push sur github
controle de la bonne execution du workflow

2.2
name: CI Backend Testing

on:
  push:
    branches:
      - main      # Trigger when code is pushed to the main branch
      - develop   # Optionally, also trigger when code is pushed to the develop branch
  pull_request:
    branches:
      - main      # Trigger when a pull request is made targeting the main branch

jobs:
  test-backend:
    runs-on: ubuntu-22.04

    steps:
      - uses: actions/checkout@v4  # Checkout the code

      # Set up JDK 21
      - name: Set up JDK 21
        uses: actions/setup-java@v3
        with:
          java-version: '21'
          distribution: 'corretto'

      # Build and test with Maven
      - name: Build and test with Maven
        run: mvn -B verify sonar:sonar -Dsonar.projectKey=malachaneG_tp-devops-correction-docker -Dsonar.organization=malachaneg -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=${{ secrets.SONAR_TOKEN }}  --file ./pom.xml
        working-directory: simple-api
le main.yml exécute les étapes suivantes chaque fois qu'un événement de push ou de pull request a lieu :

Vérification du code source avec actions/checkout@v4.
Configuration de l'environnement Java avec JDK 21 via actions/setup-java@v3.
Construction du projet et exécution des tests avec Maven, ainsi qu'une analyse de la qualité du code via SonarCloud.


2.3 Stockage centralisé : Une fois que les images sont poussées vers un registre, elles peuvent être stockées, versionnées et accessibles de n'importe où. 
Cela permet d'avoir un dépôt central où vos images Docker sont gérées.
Réutilisation : Vous pouvez tirer la même image plusieurs fois à partir de n'importe quel environnement (développement, staging, production), garantissant ainsi 
que l'application s'exécute dans le même environnement et avec la même configuration à chaque fois.
cela permet aussi une partage facile.

3.1
Inventaire :
L'inventaire définit les hôtes et les variables pour Ansible. Dans ce cas, vous créez un fichier setup.yml avec la structure suivante :

yaml
Copier
Modifier
all:
  vars:
    ansible_user: admin  # Utilisateur SSH pour se connecter aux hôtes.
    ansible_ssh_private_key_file: /etc/home/id_rsa  # Clé privée pour l'authentification SSH.
  children:
    prod:
      hosts:
        hostname_or_ip:  # Hôte ou IP du serveur de production.
Cela permet de gérer les configurations des hôtes, notamment l'utilisateur, la clé SSH, et de les regrouper sous des catégories comme prod.

Commandes de base :
Tester l'inventaire avec la commande ping : Cette commande teste la connectivité entre Ansible et les hôtes définis dans l'inventaire.

ansible all -i inventories/setup.yml -m ping
Récupérer les faits des hôtes : Cette commande récupère des informations sur les hôtes, comme la distribution du système d'exploitation.

ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"
Supprimer Apache2 avec Ansible : Cette commande supprime le serveur Apache2 des hôtes cibles.

ansible all -i inventories/setup.yml -m apt -a "name=apache2 state=absent" --become
Ces commandes permettent de gérer la configuration des serveurs, de collecter des informations sur leur état et de mettre à jour leur configuration de manière automatisée.

3.2
Voici les taches que ce playbook réalise :
Installation des prérequis pour Docker :

Installe les paquets nécessaires (apt-transport-https, ca-certificates, curl, gnupg, lsb-release, python3-venv).
Ajout de la clé GPG de Docker :

Ajoute la clé GPG officielle de Docker pour garantir l'authenticité des paquets.
Ajout du dépôt APT Docker stable :

Ajoute le dépôt officiel Docker pour Debian afin de pouvoir installer Docker depuis ce dépôt.


Installation de Docker :

Installe Docker Community Edition (docker-ce) à partir du dépôt Docker.
Installation de Python3 et pip3 :

Installe Python 3 et son gestionnaire de paquets pip3.
Création d'un environnement virtuel pour le SDK Docker :

Crée un environnement virtuel Python dans /opt/docker_venv pour isoler les dépendances Python.
Installation du SDK Docker pour Python dans l'environnement virtuel :

Installe le SDK Docker pour Python dans l'environnement virtuel créé.
Assurance que Docker est en cours d'exécution :

Vérifie et démarre le service Docker si nécessaire pour s'assurer qu'il fonctionne.
Ces tâches permettent d'installer et configurer Docker, Python, et le SDK Docker pour Python dans un environnement Debian.

3.3 chaques tasks de chaque rôle récupère l'image correspondante sur dockerhub et crée un conteneur docker et relance la création du conteneur à chaque fois que les tasks sont executé et qu'une nouvelle image est disponible sur dockerhub

