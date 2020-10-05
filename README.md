Docker

Docker permet d'exécuter des applications dans des conteneurs qui sont pour la plupart isolés de tout.
Chaque conteneur est comme une machine virtuelle individuelle manquant de tout ce qui n'est pas nécessaire pour exécuter votre application. 
Cela rend les conteneurs très légers, rapides et sécurisés.
Les conteneurs sont également destinés à être jetables. Vous pouvez le tuer et en créer un autre sans effort grâce au système d'images de conteneurs.
L'application à l'intérieur des conteneurs fonctionnera de la même manière dans tous les systèmes (Windows, Mac ou Linux). 


Avant de commencer :

- NodeJS et Docker doivent être en cours d'exécution sur la machine.
- Faire la commande "npm install" dans les dossiers Frontend et Backend pour installer toutes les dépendances nécessaires.


Conteneurisez la partie frontend :

# Utilisez une version allégée de Node comme image parente
 FROM mhart / alpine-node: 8.11.4
 
# Définissez le répertoire sur "/ client"
 WORKDIR / client
 
# copier le package.json dans le conteneur à "/ client"
 COPY package * .json / client /
 
# Installez les dependences
 RUN npm install
 
# Copiez le contenu du répertoire courant dans le conteneur à "/ client"
 COPY. /client/
 
# Rendre le port 3000 disponible au monde en dehors de ce conteneur
 EXPOSE 3000
 
# Exécutez l'application lorsque le conteneur se lance
 CMD ["npm", "start"]
 
 
Conteneurisez la partie backend :

# Utilisez une version allégée de Node comme image parente
 FROM mhart / alpine-node: 8.11.4
 
# Définissez le répertoire sur "/ api"
 WORKDIR / api
 
# Copiez package.json dans le conteneur à "/ api"
 COPY package * .json / api /
 
# Installez les dependences
 RUN npm install
 
# Copiez le contenu du répertoire actuel dans le conteneur à "/ api"
 COPY. / api /
 
# Rendre le port 8080 disponible au monde en dehors de ce conteneur
 EXPOSE 8080
 
# Exécutez l'application lorsque le conteneur se lance
 CMD ["npm", "start"]
 

Faire le Docker-Compose.yml
Compose est un outil permettant de définir et d'exécuter des applications Docker multi-conteneurs.

Il faut dire à Docker de créer un conteneur appelé client , en utilisant l'image webapp-client (qui est l'image que nous avons définie sur notre Dockerfile client) qui écoutera sur le port 3000 : 

version: "3"
services:
    client:
        image: app-client
        restart: always
        ports:
            - "3000:3000"
        volumes:
            - ./client:/client
            - /client/node_modules
        links:
            - api
        networks:
            webappnetwork
            
Ensuite, il faut dire qu'on souhaite créer un conteneur appelé api en utilisant l'image webapp-api (qui est l'image que nous avons définie sur notre API Dockerfile) qui sera à l'écoute sur le port 8080 :

    api:
        image: app-api
        restart: always
        ports:
            - "8080:8080"
        volumes:
            - ./api:/api
            - /api/node_modules
        depends_on:
            - mongodb
        networks:
            webappnetwork

On ajoute une base de données MongoDB :

mongodb:
        image: mongo
        restart: always
        container_name: mongodb
        volumes:
            - ./data-node:/data/db
        ports:
            - 27017:27017
        command: mongod --noauth --smallfiles
        networks:
            - webappnetwork

Pour créer un réseau partagé pour votre conteneur ajoutez ceux-ci :

networks:
    webappnetwork:
        driver: bridge


Faire fonctionner vos conteneurs avec les commandes :

- docker-compose build
- docker-compose up

