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

![img](https://github.com/lhdenis/deployment-folder/blob/master/architecture_kubernetes.png)

## Prerequisites

Il faut lister ce qu’il faut installer avant de lancer le projet.
Exemple :
-Java 17
-Node.js
-Angular CLI
-Docker
-Kubernetes
-Maven
<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

Use this space to list resources you find helpful and would like to give credit to. I've included a few of my favorites to kick things off!

* [Choose an Open Source License](https://choosealicense.com)
* [GitHub Emoji Cheat Sheet](https://www.webpagefx.com/tools/emoji-cheat-sheet)
* [Malven's Flexbox Cheatsheet](https://flexbox.malven.co/)
* [Malven's Grid Cheatsheet](https://grid.malven.co/)
* [Img Shields](https://shields.io)
* [GitHub Pages](https://pages.github.com)
* [Font Awesome](https://fontawesome.com)
* [React Icons](https://react-icons.github.io/react-icons/search)

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/othneildrew/Best-README-Template.svg?style=for-the-badge
[contributors-url]: https://github.com/othneildrew/Best-README-Template/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/othneildrew/Best-README-Template.svg?style=for-the-badge
[forks-url]: https://github.com/othneildrew/Best-README-Template/network/members
[stars-shield]: https://img.shields.io/github/stars/othneildrew/Best-README-Template.svg?style=for-the-badge
[stars-url]: https://github.com/othneildrew/Best-README-Template/stargazers
[issues-shield]: https://img.shields.io/github/issues/othneildrew/Best-README-Template.svg?style=for-the-badge
[issues-url]: https://github.com/othneildrew/Best-README-Template/issues
[license-shield]: https://img.shields.io/github/license/othneildrew/Best-README-Template.svg?style=for-the-badge
[license-url]: https://github.com/othneildrew/Best-README-Template/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/othneildrew
[product-screenshot]: images/screenshot.png
[Next.js]: https://img.shields.io/badge/next.js-000000?style=for-the-badge&logo=nextdotjs&logoColor=white
[Next-url]: https://nextjs.org/
[React.js]: https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB
[React-url]: https://reactjs.org/
[Vue.js]: https://img.shields.io/badge/Vue.js-35495E?style=for-the-badge&logo=vuedotjs&logoColor=4FC08D
[Vue-url]: https://vuejs.org/
[Angular.io]: https://img.shields.io/badge/Angular-DD0031?style=for-the-badge&logo=angular&logoColor=white
[Angular-url]: https://angular.io/
[Svelte.dev]: https://img.shields.io/badge/Svelte-4A4A55?style=for-the-badge&logo=svelte&logoColor=FF3E00
[Svelte-url]: https://svelte.dev/
[Laravel.com]: https://img.shields.io/badge/Laravel-FF2D20?style=for-the-badge&logo=laravel&logoColor=white
[Laravel-url]: https://laravel.com
[Bootstrap.com]: https://img.shields.io/badge/Bootstrap-563D7C?style=for-the-badge&logo=bootstrap&logoColor=white
[Bootstrap-url]: https://getbootstrap.com
[JQuery.com]: https://img.shields.io/badge/jQuery-0769AD?style=for-the-badge&logo=jquery&logoColor=white
[JQuery-url]: https://jquery.com 
