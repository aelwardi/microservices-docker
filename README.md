# LA MISE EN PLACE D'UNE Infrastructure Docker et Orchestration Avancée (2025)

## Table des Matières

[Introduction Générale](#introduction)
[Chapitre 1 : Contexte général et enjeux du projet.](#architecture-du-projet)
   - [Contexte du projet](#vue-densemble)
   - [Microservices](#microservices)
   - [Architecture](#architecture)
[Chapitre 2 : Création d'un Dockerfile pour chaque microservice et présentation des fichiers d'environnement.](#environnement-et-dépendances)
   - [FrontEnd — Préparation et Dockerisation](#environnement-dexecution)
   - [BackEnd — Préparation et Dockerisation](#logiciels-nécessaires)
   - [Bilan et justification des choix techniques](#automatisation-avec-gitlab-ci-cd)
[Chapitre 3 : Création des images, publication sur Docker Hub et gestion via Docker Compose.](#configuration-et-déploiement)
   - [Déploiement Manuel avec PM2 pour la Pré-production](#déploiement-manuel-avec-pm2-pour-la-pré-production)
   - [Déploiement avec Docker et Docker Compose](#déploiement-avec-docker-et-docker-compose)
     - [Environnement de Développement](#environnement-de-développement)
     - [Environnement de Production avec Docker Swarm](#environnement-de-production-avec-docker-swarm)
   - [Scripts d'Automatisation](#scripts-dautomatisation)
[Chapitre 4 : Configuration et déploiement par différentes méthodes (PM2, Docker Compose, Docker Swarm).](#fonctionnalités-principales)
   - [Authentification JWT](#authentification-jwt)
   - [Gestion des Produits et du Panier](#gestion-des-produits-et-du-panier)
   - [Gestion des Commandes](#gestion-des-commandes)
7. [Flux de Données](#flux-de-données)
8. [Tests et Qualité du Code](#tests-et-qualité-du-code)
   - [Tests de Sécurité](#tests-de-sécurité)
   - [Tests Frontend](#tests-frontend)
   - [Tests Backend](#tests-backend)
9. [Bonnes Pratiques et Considérations](#bonnes-pratiques-et-considérations)
10. [Annexes](#annexes)
    - [Exemples de Commandes CURL](#exemples-de-commandes-curl)
    - [Comment Exécuter le Projet](#comment-exécuter-le-projet)

---

# LA MISE EN PLACE D'UNE Infrastructure Docker et Orchestration Avancée (2025)

---

## Introduction Générale

Depuis l'aube de l'ère numérique, les avancées technologiques n'ont cessé de transformer notre manière de travailler, d'échanger et de construire des solutions informatiques. L'arrivée d'Internet, suivie par l'explosion du Cloud et des machines virtuelles, a bouleversé les habitudes : il est devenu indispensable de choisir un environnement de travail, que ce soit sur une machine locale, à distance, ou dans le Cloud. Pour chaque nouveau projet ou application, il fallait alors configurer l'environnement, installer de nombreux outils, bibliothèques et dépendances, et veiller à la compatibilité entre les différents systèmes d'exploitation.

Cette complexité initiale a souvent été source de pertes de temps, de bugs inattendus, et de difficultés lors du déploiement ou de la montée en charge d'une application. L'arrivée des conteneurs a marqué un tournant majeur : grâce à des technologies telles que Docker, il est désormais possible d’encapsuler une application et tout son environnement dans un conteneur unique, portable et reproductible. Plus besoin de se soucier des différences entre les postes des développeurs, des serveurs de production ou des configurations spécifiques de chaque machine.

Par la suite, avec l'émergence de l'architecture microservices, le besoin de gérer plusieurs conteneurs interconnectés s'est imposé. Orchestrer ces conteneurs, assurer leur communication, leur montée en charge et leur résilience est devenu un enjeu central pour tirer parti de la flexibilité offerte par le Cloud et l'informatique moderne. Des outils comme Docker Swarm, Kubernetes ou encore les services Cloud managés sont alors apparus, permettant de piloter des applications complexes avec robustesse et efficacité.

C'est dans ce contexte d’évolution continue que s'inscrit le présent projet. Face à la nécessité de proposer une plateforme moderne, scalable et facilement déployable, il a été choisi de bâtir une architecture basée sur des microservices conteneurisés, orchestrés via Docker Swarm. Ce choix vise à simplifier la gestion, le déploiement et l'évolution de l'ensemble des services applicatifs, tout en garantissant une portabilité maximale et une maintenance facilitée.

Le rapport détaille ainsi les étapes majeures de cette démarche :
- **Chapitre 1** : Contexte général et enjeux du projet.
- **Chapitre 2** : Création d’un Dockerfile pour chaque microservice et présentation des fichiers d’environnement.
- **Chapitre 3** : Création des images, publication sur Docker Hub et gestion via Docker Compose.
- **Chapitre 4** : Configuration et déploiement par différentes méthodes (PM2, Docker Compose, Docker Swarm).
- **Chapitre 5** : Démonstration et validation du fonctionnement de la solution.

À travers cette approche, l’objectif est d’illustrer comment la conteneurisation et l’orchestration transforment la façon de concevoir, livrer et exploiter des applications dans un monde où la rapidité, la fiabilité et la scalabilité sont devenues les maîtres-mots.

---

## Chapitre 1 : Contexte général et enjeux du projet.

### Contexte du projet

Le projet est structuré autour d'une architecture microservices, permettant de séparer les différentes fonctionnalités en services indépendants. Cette approche facilite la maintenance, la scalabilité et l'évolution de l'application.

**Composants Principaux :**

- **Frontend** : Application utilisateur développée avec Vue.js.
- **Backend** : Trois microservices principaux :
  - **Auth Service** : Gestion de l'authentification et des utilisateurs.
  - **Product Service** : Gestion des produits et du panier.
  - **Order Service** : Gestion des commandes.
- **Base de Données** : Chaque microservice dispose de sa propre base de données MongoDB.
- **Conteneurisation** : Utilisation de Docker et Docker Compose pour la conteneurisation et l'orchestration, avec Docker Swarm pour la production.


- Les machines du projets utilisées sont toutes des **debian 12**, 
### Microservices

1. **Auth Service** : Gère l'inscription, la connexion, la gestion des profils utilisateurs et l'authentification via JWT.
2. **Product Service** : Gère l'affichage des produits, les opérations sur le panier (ajout, modification, suppression).
3. **Order Service** : Gère la création et la consultation des commandes des utilisateurs.

---

### Architecture

L'architecture du projet repose sur la séparation stricte des responsabilités via le modèle microservices, chaque composant étant développé, testé, déployé et maintenu de manière indépendante. Cette organisation permet une évolution rapide, une meilleure fiabilité et une adaptabilité accrue de l'ensemble de la solution.

**Schéma global :**

```
[ Utilisateur ]
      |
      v
+--------------------+
|     Frontend       |  (Vue.js)
+--------------------+
      |
      v
+-------------------------------+
|          API Gateway          | (optionnel/server.cjs)
+-------------------------------+
      |         |         |
      v         v         v
+----------+ +----------+ +----------+
|  Auth    | | Product  | |  Order   |  (Express.js)
| Service  | | Service  | | Service  |
+----------+ +----------+ +----------+
      |         |         |
      v         v         v
+----------+ +----------+ +----------+
| MongoDB  | | MongoDB  | | MongoDB  |
+----------+ +----------+ +----------+
```

**Points clés :**
- **Isolation** : chaque service possède sa propre base de données MongoDB, évitant les dépendances croisées et facilitant la gestion des données.
- **Interopérabilité** : les échanges entre le frontend et les microservices se font via des APIs REST sécurisées (JWT).
- **Conteneurisation** : l’ensemble des services et bases de données sont packagés dans des conteneurs Docker, orchestrés via Docker Compose en développement et Docker Swarm en production.
- **Scalabilité** : chaque microservice peut être dupliqué et mis à l’échelle indépendamment selon la charge ou les besoins métiers.

Cette architecture permet de garantir à la fois robustesse, flexibilité et facilité de maintenance pour l’application e-commerce, tout en tirant parti des meilleures pratiques du Cloud moderne.

## Chapitre 2 : Création d'un Dockerfile pour chaque microservice et présentation des fichiers d'environnement.

### FrontEnd — Préparation et Dockerisation

1. **Création et organisation des fichiers nécessaires**
- ***Se placer dans le dossier du frontend***
```bash
cd frontend
```
- ***Création du `Dockerfile`***
Crée un fichier `Dockerfile` et copie le contenu suivant :

```bash
touch Dockerfile
```

```dockerfile
# Étape 1 : Image de base pour installer les dépendances
FROM node:20-alpine AS base
WORKDIR /app
COPY package*.json ./
RUN npm ci

# Étape 2 : Construction de l'application (build)
FROM base AS build
COPY . .
RUN npm run build

# Étape 3 : Développement (hot-reload, tests...)
FROM base AS development
WORKDIR /app
COPY . .
EXPOSE 5173 8080
CMD ["npm", "run", "dev"]

# Étape 4 : Production (image allégée, uniquement le build)
FROM node:20-alpine AS production
WORKDIR /app
COPY --from=build /app/dist ./dist
COPY --from=build /app/server.cjs ./server.cjs
COPY --from=build /app/package*.json ./
RUN npm ci --only=production
# Création d'un utilisateur non-root par sécurité
RUN addgroup -S gelwardi && adduser -S elwardi1 -G gelwardi && chown -R elwardi1:gelwardi /app
USER elwardi1
EXPOSE 8080
CMD ["node", "server.cjs"]
```
***Explications*** :
- **base** : installe les dépendances communes.
- **build** : construit l’application pour la production (`npm run build`).
- **development** : image pour le développement (hot-reload sur les ports 5173 ou 8080).
- **production** : image optimisée, ne contient que le build et le serveur Node pour servir l’app. Utilise un utilisateur non-root pour plus de sécurité.

---

- ***Création du fichier `.dockerignore`***
Crée un fichier `.dockerignore` pour éviter de copier des fichiers inutiles dans l’image :

```bash
touch .dockerignore
```

```
node_modules
dist
```

---

- ***Création des fichiers d’environnement***
Crée un fichier `.env` pour le développement :

```bash
touch .env
```

```
PORT=8080
MONGODB_URI=mongodb://mongodb:27017/ecommerce
JWT_SECRET=efrei_super_pass
VITE_AUTH_SERVICE_URL=http://auth-service:3001
VITE_PRODUCT_SERVICE_URL=http://product-service:3000
VITE_ORDER_SERVICE_URL=http://order-service:3002
NODE_ENV=development
```

Crée un fichier `.env.production` pour la production :

```bash
touch .env.production
```

```
PORT=8080
MONGODB_URI=mongodb://mongodb:27017/ecommerce
JWT_SECRET=efrei_super_pass
VITE_AUTH_SERVICE_URL=http://auth-service:3001
VITE_PRODUCT_SERVICE_URL=http://product-service:3000
VITE_ORDER_SERVICE_URL=http://order-service:3002
NODE_ENV=production
```

***Explications*** :
- Les variables `VITE_*` sont injectées côté frontend pour accéder aux microservices.
- `NODE_ENV` permet de distinguer les modes développement/production.
- Les valeurs peuvent être adaptées selon l'environnement (ex: mots de passe, URLs…).

---
**Résumé du process**

| Étape                | Commande/File                   | Rôle                                                                 |
|----------------------|---------------------------------|---------------------------------------------------------------------|
| Aller dans frontend  | `cd frontend`                   | Se placer au bon endroit                                            |
| Dockerfile           | `touch Dockerfile`              | Définir la construction multi-stage pour dev/prod                   |
| .dockerignore        | `touch .dockerignore`           | Ignorer les fichiers inutiles dans l’image Docker                   |
| .env                 | `touch .env`                    | Variables d’environnement pour le développement                     |
| .env.production      | `touch .env.production`         | Variables d’environnement pour la production                        |

---

**Bonnes pratiques appliquées pour la Dockerisation du Frontend**
Dans la mise en place de la conteneurisation du frontend, j’ai systématiquement veillé à appliquer les bonnes pratiques suivantes :
- ***Utilisation d’un utilisateur non-root en production*** :  
  Dans le Dockerfile, j’ai créé un utilisateur dédié (non-root) pour exécuter l’application en production. Cela permet de réduire les risques de sécurité en cas de compromission du conteneur.
- ***Gestion sécurisée des secrets et variables d’environnement*** :
  Les variables d’environnement sont gérées localement, ou injectées lors du déploiement.
- ***Optimisation des images grâce à `.dockerignore`***
  J’ai configuré un fichier `.dockerignore` pour exclure les dossiers et fichiers inutiles (tels que `node_modules` ou `dist`) lors de la construction de l’image Docker, ce qui permet de garder l’image légère et d’éviter d’inclure des fichiers pouvant contenir des informations sensibles.
- ***Tests des deux modes de fonctionnement***
  J’ai pris soin de tester le projet à la fois en mode développement (`npm run dev`) et en mode production (`node server.cjs`) pour m’assurer du bon fonctionnement dans les deux contextes, et garantir ainsi la fiabilité du processus de build multi-stage.


### BackEnd — Préparation et Dockerisation

Pour chacun des microservices backend (`auth-service`, `order-service`, `product-service`), j’ai appliqué une démarche identique de préparation, de configuration et de dockerisation, en veillant à respecter les meilleures pratiques de sécurité, de performance et de maintenabilité.

1. **auth-service**
- ***Accès au dossier du service :***
```bash
  cd services/auth-service
```

- ***Création du Dockerfile :***
```bash
  touch Dockerfile
```

```dockerfile
  FROM node:20-alpine AS base
  WORKDIR /app
  COPY package*.json ./

  # Développement
  FROM base AS development
  ENV NODE_ENV=development
  RUN npm install
  COPY . .
  EXPOSE 3001
  CMD ["npm", "run", "dev"]

  # Build production
  FROM base AS build
  ENV NODE_ENV=production
  RUN npm ci --omit=dev
  COPY . .
  RUN npm run build || true

  # Image finale production, sécurisée
  FROM node:20-alpine AS production
  WORKDIR /app
  ENV NODE_ENV=production
  COPY --from=build /app ./
  RUN addgroup -S gelwardi && adduser -S elwardi1 -G gelwardi
  USER elwardi1
  EXPOSE 3001
  CMD ["node", "src/app.js"]
```

***Explications :***
  - J’utilise un build multi-stage : un pour le dev, un pour le build, un pour la prod.
  - En dev : installation complète des dépendances pour le hot reload, port 3001 exposé.
  - En prod : installation uniquement des dépendances nécessaires, exécution de l’app avec un utilisateur non-root pour la sécurité.

- ***Fichiers d’environnement :***
  - `.env` (développement) :
```bash
  touch .env
```
    ```
    PORT=3001
    MONGODB_URI=mongodb://mongodb:27017/auth
    JWT_SECRET=efrei_super_pass
    NODE_ENV=development
    ```
  - `.env.production` (production) :
```bash
  touch .env.production
```
    ```
    PORT=3001
    MONGODB_URI=mongodb://mongodb:27017/auth
    JWT_SECRET=efrei_super_pass
    NODE_ENV=production
    ```
  **Pourquoi ?**  
  Cela me permet de séparer les variables sensibles et les configurations selon l’environnement.

---

2. **order-service**
- ***Accès au dossier du service :***
```bash
  cd services/order-service
```
- ***Création du Dockerfile :***
  ```dockerfile
  FROM node:20-alpine AS base
  WORKDIR /app
  COPY package*.json ./

  # Développement
  FROM base AS development
  ENV NODE_ENV=development
  RUN npm install
  COPY . .
  EXPOSE 3002
  CMD ["npm", "run", "dev"]

  # Build production
  FROM base AS build
  ENV NODE_ENV=production
  RUN npm ci --omit=dev
  COPY . .
  RUN npm run build || true

  # Image finale production
  FROM node:20-alpine AS production
  WORKDIR /app
  ENV NODE_ENV=production
  COPY --from=build /app/package*.json ./
  COPY --from=build /app/node_modules ./node_modules
  COPY --from=build /app/src ./src
  COPY --from=build /app/*.js ./
  RUN addgroup -S gelwardi && adduser -S elwardi1 -G gelwardi
  USER elwardi1
  EXPOSE 3002
  CMD ["node", "src/app.js"]
  ```

***Explications :***
  - Structure similaire : séparation dev/build/prod.
  - Je copie seulement le nécessaire en production (node_modules, src, configs), ce qui réduit la taille de l’image et le temps de build.
  - L’utilisateur non-root est utilisé ici aussi.

- ***Fichiers d’environnement :***
  - `.env` (développement) :
```bash
  touch .env
```
    ```
    PORT=3002
    MONGODB_URI=mongodb://mongodb:27017/orders
    JWT_SECRET=efrei_super_pass
    VITE_PRODUCT_SERVICE_URL=http://product-service:3000
    NODE_ENV=development
    ```
  - `.env.production` (production) :
```bash
  touch .env.production
```
    ```
    PORT=3002
    MONGODB_URI=mongodb://mongodb:27017/orders
    JWT_SECRET=efrei_super_pass
    VITE_PRODUCT_SERVICE_URL=http://product-service:3000
    NODE_ENV=production
    ```
  ***Pourquoi ?***  
  J’ai inclus l’URL du product-service pour permettre l’interconnexion des microservices via les variables d’environnement.

---

3. **product-service**
- ***Accès au dossier du service :***
```bash
  cd services/product-service
```
- ***Création du Dockerfile :***
```dockerfile
  FROM node:20-alpine AS base
  WORKDIR /app
  COPY package*.json ./

  # Développement
  FROM base AS development
  ENV NODE_ENV=development
  RUN npm install
  COPY . .
  EXPOSE 3000
  CMD ["npm", "run", "dev"]

  # Build production
  FROM base AS build
  ENV NODE_ENV=production
  RUN npm ci --omit=dev
  COPY . .
  RUN npm run build || true

  # Image finale production
  FROM node:20-alpine AS production
  WORKDIR /app
  ENV NODE_ENV=production
  COPY --from=build /app ./
  RUN addgroup -S gelwardi && adduser -S elwardi1 -G gelwardi
  USER elwardi1
  EXPOSE 3000
  CMD ["node", "src/app.js"]
```

***Explications :***
- Même logique : build multi-stage, nettoyage de l’image finale, sécurité utilisateur.
- Port 3000 exposé pour ce service.

- ***Fichiers d’environnement :***
  - `.env` (développement) :
```bash
  touch .env
```
```
    PORT=3000
    MONGODB_URI=mongodb://mongodb:27017/ecommerce
    JWT_SECRET=efrei_super_pass
    NODE_ENV=development
    ```
  - `.env.production` (production) :
```bash
  touch .env.production
```
    ```
    PORT=3000
    MONGODB_URI=mongodb://mongodb:27017/ecommerce
    JWT_SECRET=efrei_super_pass
    NODE_ENV=production
    ```
---

### Bilan et justification des choix techniques
**Pourquoi cette organisation ?**
- ***Multi-stage builds*** : Pour optimiser la taille des images Docker, ne garder en production que le strict nécessaire, et accélérer les déploiements.
- ***Séparation dev/prod*** : Facilite le travail en équipe, les tests et le déploiement, tout en évitant les erreurs de configuration.
- ***Sécurité*** : Utilisation d’un utilisateur non-root dans toutes les images de production.
- ***Maintenabilité*** : Chaque microservice est isolé, ce qui permet de faire évoluer ou corriger un service sans impacter les autres.
- ***Environnement reproductible*** : Grâce aux fichiers `.env` spécifiques à chaque service et chaque environnement, le déploiement est fiable et les secrets sont protégés.


## Chapitre 3 : Création des images, publication sur Docker Hub et gestion via Docker Compose.

### Création des images, publication sur Docker Hub
Dans cette étape, j’ai construit les images Docker pour chaque composant de l’application (frontend et microservices), puis je les ai taguées et poussées sur mon dépôt Docker Hub. Voici le détail de chaque étape :

---

1. **Création des images**
Pour chaque service, j’ai utilisé la commande `docker build` en spécifiant plusieurs tags : un tag de version (`1.0.1`), un tag de version majeure (`1.0`) et le tag `latest`.  
Cela permet de suivre les évolutions, de revenir facilement à une version précise si besoin, et de toujours disposer d’un tag générique pour le déploiement automatique.

- ***Pour le frontend :***
```bash
docker build -t aelwardi1/e-commerce-frontend:1.0.1 \
             -t aelwardi1/e-commerce-frontend:1.0 \
             -t aelwardi1/e-commerce-frontend:latest \
             -f ./frontend/Dockerfile --target production ./frontend
```
*Le build s’est bien déroulé, toutes les étapes ont été validées (voir logs ci-dessous pour un extrait).*
- ***Pour le product-service :***
```bash
docker build -t aelwardi1/e-commerce-product-service:1.0.1 \
             -t aelwardi1/e-commerce-product-service:1.0 \
             -t aelwardi1/e-commerce-product-service:latest \
             -f ./services/product-service/Dockerfile --target production ./services/product-service
```
- ***Pour le auth-service :***
```bash
docker build -t aelwardi1/e-commerce-auth-service:1.0.1 \
             -t aelwardi1/e-commerce-auth-service:1.0 \
             -t aelwardi1/e-commerce-auth-service:latest \
             -f ./services/auth-service/Dockerfile --target production ./services/auth-service
```
- ***Pour le order-service :***
```bash
docker build -t aelwardi1/e-commerce-order-service:1.0.1 \
             -t aelwardi1/e-commerce-order-service:1.0 \
             -t aelwardi1/e-commerce-order-service:latest \
             -f ./services/order-service/Dockerfile --target production ./services/order-service
```

---
***Sortie et validation du build***
Après chaque build, Docker affiche un ensemble d’étapes validant la construction, l’assemblage des couches, le nommage et le push local de l’image.  
Extrait de logs pour le frontend :
```
 => [internal] load build definition from Dockerfile
 => [internal] load metadata for docker.io/library/node:20-alpine
 => [base 1/4] FROM docker.io/library/node:20-alpine
 => ...
 => => naming to docker.io/aelwardi1/e-commerce-frontend:1.0.1
 => => naming to docker.io/aelwardi1/e-commerce-frontend:1.0
 => => naming to docker.io/aelwardi1/e-commerce-frontend:latest
```
Les builds pour les microservices sont similaires, chaque étape étant validée et l’image étant prête à être poussée.

---

2. **Publication sur Docker Hub**
Après avoir construit les images Docker pour le frontend et chaque microservice backend, je les ai publiées sur mon espace Docker Hub. Cette étape permet de rendre les images accessibles à toute l’équipe et de faciliter le déploiement sur n’importe quel serveur ou orchestrateur.

- ***FrontEnd***
Pour le frontend, j’ai poussé trois tags différents : `1.0.1`, `1.0`, et `latest` pour suivre les versions et permettre un déploiement automatisé avec le tag `latest`.
```bash
docker push aelwardi1/e-commerce-frontend:1.0.1
docker push aelwardi1/e-commerce-frontend:1.0
docker push aelwardi1/e-commerce-frontend:latest
```
***Extrait de sortie :***
```
The push refers to repository [docker.io/aelwardi1/e-commerce-frontend]
301aebe3d905: Waiting
...
1.0.1: digest: sha256:483bd2808e9bf093e9eec877877bc34b2e57bc1d898a23a8ec8c7ce8daab044f size: 856
```
*Les couches déjà existantes ne sont pas repoussées, seules les nouvelles sont transférées, ce qui accélère les publications.*

---

- ***BackEnd***
1. ***Product-Service***
```bash
docker push aelwardi1/e-commerce-product-service:1.0.1
docker push aelwardi1/e-commerce-product-service:1.0
docker push aelwardi1/e-commerce-product-service:latest
```
**Extrait de sortie :**
```
The push refers to repository [docker.io/aelwardi1/e-commerce-product-service]
e8399866283b: Pushed
...
1.0.1: digest: sha256:6e0674c4e5a9439caf6a99c97317aa247f25cc817fa4ee4107bf899853fa7c7c size: 856
```
2. ***Auth-Service***
```bash
docker push aelwardi1/e-commerce-auth-service:1.0.1
docker push aelwardi1/e-commerce-auth-service:1.0
docker push aelwardi1/e-commerce-auth-service:latest
```
**Extrait de sortie :**
```
The push refers to repository [docker.io/aelwardi1/e-commerce-auth-service]
a282b930871b: Pushed
...
1.0.1: digest: sha256:7558491194e9ee16ef6a45dff060a6bc537a7ee09cbad3cff2d96e6dd0a262be size: 856
```

3. ***Order-Service***
```bash
docker push aelwardi1/e-commerce-order-service:1.0.1
docker push aelwardi1/e-commerce-order-service:1.0
docker push aelwardi1/e-commerce-order-service:latest
```
**Extrait de sortie :**
```
The push refers to repository [docker.io/aelwardi1/e-commerce-order-service]
e61a25c39866: Waiting
...
1.0.1: digest: sha256:cf3219ecee809a3d1e157cc71edbbac19b64a7c68e89bdc3682c328eceb69929 size: 856
```

---

#### **Analyse et justification**

- ***Pourquoi plusieurs tags ?***  
  Utiliser plusieurs tags (`<version>`, `<version majeure>`, `latest`) permet de :
  - Faciliter le rollback en cas de bug,
  - S’adapter aux différents environnements,
  - Automatiser les déploiements sur des environnements de recette ou de production.

- ***Pourquoi Docker Hub ?***  
  Docker Hub est une plateforme centralisée, fiable et largement supportée par tous les outils CI/CD et orchestrateurs. Cela évite de devoir reconstruire les images sur chaque machine et garantit une cohérence totale entre les environnements.

---

3. **Création des fichiers Docker Compose**
Pour orchestrer l’ensemble de l’application, j’ai mis en place deux fichiers `docker-compose` : un pour le développement (`docker-compose.yaml`) et un pour la production (`docker-compose.prod.yaml`).  
Chacun adapte les bonnes pratiques selon l’environnement et permet un déploiement rapide, reproductible et multi-service.
- ***Fichier docker-compose.yaml (développement local)***
Création du fichier :
```bash
touch docker-compose.yaml
```

```docker-compose
version: "3.9"
services:
  mongodb:
      image: mongo:6
      container_name: mongodb
      ports:
        - "27017:27017"
      volumes:
        - mongodb_data:/data/db
      healthcheck:
        test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
        interval: 10s
        timeout: 5s
        retries: 5
      networks:
        - backend
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
      target: development
    container_name: frontend
    volumes:
      - ./frontend:/app
      - /app/node_modules
    ports:
      - "8080:8080"
    env_file:
      - ./frontend/.env
    depends_on:
      product-service:
        condition: service_healthy
        restart: true
      auth-service:
        condition: service_healthy
        restart: true
      order-service:
        condition: service_healthy
        restart: true
    networks:
      - frontend
      - backend

  product-service:
    build: 
      context: ./services/product-service
      dockerfile: Dockerfile
      target: development
    container_name: product-service
    volumes:
      - ./services/product-service:/app
      - /app/node_modules
    ports:
      - "3000:3000"
    depends_on:
      mongodb:
        condition: service_healthy
        restart: true
    env_file:
      - ./services/product-service/.env
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:3000/api/health"] 
      interval: 1m30s
      timeout: 30s
      retries: 5
      start_period: 30s
    networks:
      - backend

  auth-service:
    build: 
      context: ./services/auth-service
      dockerfile: Dockerfile
      target: development
    container_name: auth-service
    volumes:
      - ./services/auth-service:/app
      - /app/node_modules
    ports:
      - "3001:3001"
    depends_on:
      mongodb:
        condition: service_healthy
        restart: true
    env_file:
      - ./services/auth-service/.env
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:3001/api/health"]
      interval: 1m30s
      timeout: 30s
      retries: 5
      start_period: 30s
    networks:
      - backend

  order-service:
    build: 
      context: ./services/order-service
      dockerfile: Dockerfile
      target: development
    container_name: order-service
    volumes:
      - ./services/order-service:/app
      - /app/node_modules
    ports:
      - "3002:3002"
    depends_on:
      mongodb:
        condition: service_healthy
        restart: true
    env_file:
      - ./services/order-service/.env
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:3002/api/health"]
      interval: 1m30s
      timeout: 30s
      retries: 5
      start_period: 30s
    networks:
      - backend

networks:
  frontend:
  backend:

volumes:
  mongodb_data:
```
***Contenu et explications :***
- ***mongodb*** : Lance le conteneur MongoDB, expose le port 27017, sauvegarde les données dans un volume dédié, et réalise un healthcheck pour garantir que la base est opérationnelle avant de lancer les autres services.
- ***frontend*** : 
  - Build à partir du dossier local (`./frontend`), en mode développement (hot-reload possible).
  - Montre le code source local à l’intérieur du conteneur (volumes) pour un développement itératif.
  - Attend que les microservices soient “healthy” avant de démarrer grâce à `depends_on`.
- ***product-service, auth-service, order-service*** :
  - Chacun utilise le build local (mode développement), monte les sources en volume, expose son port, utilise un healthcheck et dépend de MongoDB.
- ***Réseaux et volumes*** : 
  - Deux réseaux (frontend et backend) assurent l’isolation logique.
  - Un volume `mongodb_data` garantit la persistance des données MongoDB.

---
- ***Fichier docker-compose.prod.yaml (production/Swarm)***
Ce fichier est destiné à l’orchestration en production, idéalement avec Docker Swarm.

```bash
touch docker-compose.yaml
```

```docker-compose
version: "3.9"
services:
  mongodb:
    image: mongo:6
    container_name: mongodb
    ports:
      - "27017:27017"
    volumes:
      - mongodb_data:/data/db
    networks:
      - backend
    healthcheck:
      test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
      interval: 10s
      timeout: 5s
      retries: 5

  frontend:
    image: aelwardi1/e-commerce-frontend:${IMAGE_TAG:-latest}
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
    ports:
      - "8080:8080"
    networks:
      - frontend
      - backend
    env_file:
      - ./frontend/.env.production
    depends_on:
      - product-service
      - auth-service
      - order-service

  product-service:
    image: aelwardi1/e-commerce-product-service:${IMAGE_TAG:-latest}
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
    ports:
      - "3000:3000"
    depends_on:
      - mongodb
    networks:
      - backend
    env_file:
      - ./services/product-service/.env.production
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:3000/api/health"] 
      interval: 1m30s
      timeout: 30s
      retries: 5
      start_period: 30s

  auth-service:
    image: aelwardi1/e-commerce-auth-service:${IMAGE_TAG:-latest}
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
    ports:
      - "3001:3001"
    depends_on:
      - mongodb
    networks:
      - backend
    env_file:
      - ./services/auth-service/.env.production
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:3001/api/health"] 
      interval: 1m30s
      timeout: 30s
      retries: 5
      start_period: 30s

  order-service:
    image: aelwardi1/e-commerce-order-service:${IMAGE_TAG:-latest}
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
    ports:
      - "3002:3002"
    depends_on:
      - mongodb
    networks:
      - backend
    env_file:
      - ./services/order-service/.env.production
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:3002/api/health"] 
      interval: 1m30s
      timeout: 30s
      retries: 5
      start_period: 30s

networks:
  frontend:
  backend:
volumes:
  mongodb_data:
```
- ***mongodb*** : Même configuration que pour le dev, mais sans montage de code local.
- ***frontend, product-service, auth-service, order-service*** :
  - Utilisent les images Docker publiées sur Docker Hub, identifiées par la variable `${IMAGE_TAG}` (qui peut être définie dans l’environnement ou la CI/CD).
  - Chaque service est répliqué (scalabilité) grâce à la directive `deploy.replicas`.
  - Les stratégies de redémarrage (`restart_policy`) garantissent une haute disponibilité.
  - Les `depends_on` sont simplifiés, mais chaque service dépend de MongoDB (pour les backends) et des microservices nécessaires (pour le frontend).
  - Les fichiers `.env.production` sont utilisés pour injecter les variables d’environnement adaptées à la production.
- ***Réseaux et volumes*** : Toujours deux réseaux logiques et un volume pour MongoDB.

---

***Résumé des différences***

| Fichier                  | Usage                 | Build local/code monté | Images Docker Hub | Scalabilité | Pour CI/CD |
|--------------------------|-----------------------|-----------------------|-------------------|-------------|------------|
| docker-compose.yaml      | Développement local   | Oui                   | Non               | Non         | Non        |
| docker-compose.prod.yaml | Production/Swarm/CI   | Non                   | Oui               | Oui         | Oui        |

---

#### **Pourquoi cette organisation ?**
- ***Séparation dev/prod*** : Permet de travailler confortablement en local avec du hot-reload et de déployer en prod avec des images optimisées.
- ***Santé des services*** : Les healthchecks garantissent que chaque service ne démarre que si ses dépendances sont prêtes.
- ***Scalabilité/haute dispo*** : Le mode Swarm active la réplication automatique des services critiques.
- ***Simplicité de maintenance*** : Changement de config ou montée de version = modification d’un tag ou d’un fichier, pas besoin de toucher à tout le projet.

---

