<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>

<!-- ABOUT THE PROJECT -->
## About The Project

Application full stack de livraison de repas construite avec Angular, Spring Boot, Kubernetes et AWS.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Fonctionnalités principales

- afficher des restaurants
- consulter un catalogue de plats
- passer une commande
- gérer les utilisateurs

## Built With

This section should list any major frameworks/libraries used to bootstrap your project. Leave any add-ons/plugins for the acknowledgements section. Here are a few examples.

- Frontend : Angular
- Backend : Spring Boot
- Base de données : MySQL / MongoDB
- Conteneurisation : Docker
- Orchestration : Kubernetes / EKS
- CI/CD : Jenkins / Argo CD

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- GETTING STARTED -->
## Architecture du projet

### 1. Frontend

Le frontend est une application Angular. Son rôle est de fournir l’interface utilisateur avec plusieurs pages, notamment : la liste des restaurantsle catalogue des plats d’un restaurantla page de commandeDonc côté client, l’utilisateur interagit uniquement avec Angular.

### 2. Backend en microservices 

Le backend est découpé en 4 microservices métiers :
-restaurant listing service : gère les restaurants
-food catalog service : gère les plats d’un restaurant
-user service : gère les informations utilisateur
-order service : gère les commandes

À cela s’ajoute Eureka Server, utilisé pour la service discovery entre microservices. Donc l’architecture backend n’est pas monolithique : chaque responsabilité métier est isolée dans son propre service.

### 3. Bases de données

Le projet utilise deux types de stockage :

#### 1) Base relationnelle : Les données relationnelles sont stockées sur Amazon RDS.
Cela concerne notamment les services qui ont besoin de données SQL.

#### 2) Base NoSQL : Le order service utilise une base MongoDB Atlas.

Donc l’architecture mélange SQL pour certains services, NoSQL pour les commandes.
C’est un choix d’architecture par service, pas une base unique pour tout le système.

### 4. Déploiement cloud

L’application est déployée sur AWS. Les applications sont dockerisées puis déployées dans un cluster Kubernetes managé par AWS, c'est à dire EKS.
L’idée est que chaque microservice est une image Docker et chaque image est exécutée dans Kubernetes sous forme de pods.

![img](https://github.com/lhdenis/deployment-folder/blob/master/architecture_kubernetes.png)

### 5. Réseau et exposition

Pour exposer l’application, on utilise un load balancer AWS comme point d’entrée principal.
Son rôle est de recevoir le trafic externe, répartir la charge, router les requêtes vers le bon composant
Le frontend devient la route par défaut, puis le routage se fait vers les microservices selon les URLs.
Donc l’utilisateur n’appelle pas directement chaque pod ou chaque microservice : il passe par un point d’entrée externe unique.

### 6. Qualité et tests

Avant le déploiement, on met en place des tests unitaires JUnit, des contrôles qualité avec SonarQube. Le but est de vérifier le code, mesurer la qualité, éviter de déployer un code cassé

### 7. Pipeline CI/CD

Le code est poussé sur GitHub, puis une chaîne CI/CD prend le relais.

La partie CI est gérée par Jenkins, installé sur une instance EC2. Jenkins récupère le code, build l’application, lance les tests, applique les contrôles qualité
La partie CD est gérée par Argo CD. Argo CD récupère l’état voulu, déploie automatiquement les mises à jour vers le cluster Kubernetes

![img](https://github.com/lhdenis/deployment-folder/blob/master/pipeline_jenkins.png)
