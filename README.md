# TP 1 : Prise en main de Docker

## Docker CLI

- **Lister les images présentes sur l'hôte Docker**
  ```sh
  docker images
  ```

- **Lister les conteneurs en cours de fonctionnement**
  ```sh
  docker ps
  ```

- **Lister tous les conteneurs, y compris ceux arrêtés**
  ```sh
  docker ps -a
  ```

- **Supprimer tous les conteneurs arrêtés**
  ```sh
  docker container prune
  ```

- **Trouver le nombre de layers présents dans une image**
  ```sh
  docker history <nom_image>
  ```

- **Lister proprement toutes les commandes disponibles dans Docker CLI**
  ```sh
  docker --help
  ```

- **Connaître la place prise sur disque par les images, conteneurs et volumes**
  ```sh
  docker system df
  ```

- **Suivre en temps réel la consommation CPU, RAM et disque des conteneurs actifs**
  ```sh
  docker stats
  ```

## Les conteneurs

- **Créer un conteneur Ubuntu et utiliser le bash**
  ```sh
  docker run -it ubuntu bash
  ```

- **Créer un conteneur Ubuntu nommé `bubun` et utiliser le bash**
  ```sh
  docker run -it --name bubun ubuntu bash
  ```

- **Arrêter le conteneur `bubun`**
  ```sh
  docker stop bubun
  ```

- **Lancer en mode détaché un conteneur nginx accessible sur le port 9876 et nommé `ginginx`**
  ```sh
  docker run -d -p 9876:80 --name ginginx nginx
  ```

- **Se connecter au conteneur `ginginx` et accéder à un terminal**
  ```sh
  docker exec -it ginginx bash
  ```

- **Lister tous les processus tournant dans `ginginx` (comme `top` sous Linux)**
  ```sh
  docker top ginginx
  ```

- **Afficher un site web statique dont les fichiers se trouvent sur la machine hôte**
  ```sh
  docker run -d -p 8080:80 -v $(pwd)/site:/usr/share/nginx/html nginx
  ```

- **Démarrer un conteneur de supervision de l'instance Docker (ou du SWARM auquel elle appartient)**
  ```sh
  docker run -d -p 8080:8080 --name cadvisor google/cadvisor
  ```

## Dockerfiles

- **Créer une image qui contienne un serveur Apache et qui serve un fichier toto.html sur le port 80**
  ```dockerfile
  FROM httpd:latest
  COPY toto.html /usr/local/apache2/htdocs/index.html
  EXPOSE 80
  ```
  ```sh
  docker build -t apache-toto .
  docker run -d -p 80:80 --name apache-container apache-toto
  ```

- **Créer une image qui contienne tout un petit site HTML et qui le serve sur le port 80**
  ```dockerfile
  FROM httpd:latest
  COPY site/ /usr/local/apache2/htdocs/
  EXPOSE 80
  ```
  ```sh
  docker build -t apache-site .
  docker run -d -p 80:80 --name apache-site-container apache-site
  ```

- **Créer un Dockerfile contenant un fichier index.html et qui refusera de fonctionner si le fichier HTML n'existe pas**
  ```dockerfile
  FROM httpd:latest
  COPY index.html /usr/local/apache2/htdocs/
  RUN test -f /usr/local/apache2/htdocs/index.html || exit 1
  EXPOSE 80
  ```
  ```sh
  docker build -t apache-index .
  docker run -d -p 80:80 --name apache-index-container apache-index
  ```

- **Créer une image contenant tous vos outils d'analyse/scan/pentest préférés (wireshark ? sonarQube ?)**
  ```dockerfile
  FROM kali:latest
  RUN apt update && apt install -y wireshark nmap sqlmap
  EXPOSE 22
  ```
  ```sh
  docker build -t pentest-tools .
  docker run -d --name pentest-container pentest-tools
  ```