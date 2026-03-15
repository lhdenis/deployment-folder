<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#about-the-project">About the Project</a></li>
    <li><a href="#key-features">Key Features</a></li>
    <li><a href="#built-with">Built With</a></li>
    <li><a href="#project-architecture">Project Architecture</a></li>
  </ol>
</details>

## About the Project

This project is a full-stack food delivery application built with **Angular**, **Spring Boot**, **Docker**, **Kubernetes**, and **AWS**.

It demonstrates how to design, containerize, deploy, and automate a modern microservices-based application using a cloud-native architecture.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Key Features

- Display a list of restaurants
- Browse a restaurant’s food catalog
- Place an order through a complete end-to-end flow
- Manage user-related data through a dedicated microservice
- Deploy the application on AWS with Kubernetes
- Automate build, quality checks, and deployment through CI/CD

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Built With

- **Frontend:** Angular
- **Backend:** Spring Boot
- **Service Discovery:** Eureka Server
- **Databases:** MySQL (Amazon RDS) / MongoDB Atlas
- **Containerization:** Docker
- **Orchestration:** Kubernetes / Amazon EKS
- **CI:** Jenkins
- **CD:** Argo CD
- **Code Quality:** JUnit / SonarQube
- **Cloud Platform:** AWS

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Project Architecture

### 1. Frontend

The frontend is a **Single Page Application (SPA)** built with Angular.

It is organized into several functional modules:
- **Header module**: shared layout displayed across the application
- **Restaurant listing module**: displays all available restaurants
- **Food catalog module**: displays the menu of a selected restaurant
- **Order summary module**: displays the cart summary and allows the user to place an order

The frontend communicates with the backend through **Angular services** using HTTP calls.
Each page only requests the data it needs from the corresponding backend microservice.

For example:
- the restaurant listing page calls the `restaurant-listing-service`
- the food catalog page calls the `food-catalog-service`
- the order summary page sends the final order to the `order-service`

This frontend does **not** access any database directly.  
All communication is handled through REST APIs exposed by the Spring Boot microservices.

---

### 2. Backend Microservices

The backend follows a **microservices architecture** built with Spring Boot.

It is split into four business services:

- **restaurant-listing-service**  
  Responsible for restaurant data such as name, address, city, and description.

- **food-catalog-service**  
  Responsible for food items associated with a restaurant.  
  It also aggregates restaurant details when needed, so the frontend can receive both restaurant information and menu items through a single API call.

- **user-service**  
  Responsible for user-related information such as address and city.  
  In this MVP, there is no dedicated user interface for authentication or profile management. Instead, the `order-service` uses the `user-service` internally to enrich the order with user details.

- **order-service**  
  Responsible for receiving and saving the final order.  
  It receives the selected food items, the restaurant details, and a user identifier, then enriches the order with user information before saving it.

This separation ensures:
- better modularity
- clearer ownership of business logic
- easier scalability
- easier maintenance

---

### 3. Service Discovery with Eureka

The project uses **Eureka Server** for **service discovery** between backend microservices.

Instead of hardcoding the address of one service inside another, services register themselves in Eureka and can discover each other dynamically.

This is especially useful in distributed systems where:
- services may restart
- network locations may change
- multiple instances may be deployed

In this project:
- `food-catalog-service` can retrieve restaurant details through service discovery
- `order-service` can call `user-service` without relying on fixed IP addresses

Eureka is therefore an **infrastructure component**, not a business service.

---

### 4. Databases

The project uses **polyglot persistence**, meaning different services use different storage technologies depending on their needs.

#### Relational database: Amazon RDS (MySQL)

The following services use **MySQL on Amazon RDS**:
- `restaurant-listing-service`
- `food-catalog-service`
- `user-service`

These services manage structured, relational data, which fits well with SQL tables and traditional entity relationships.

Examples:
- restaurants
- food items
- users

#### NoSQL database: MongoDB Atlas

The `order-service` uses **MongoDB Atlas**.

This is relevant because an order naturally contains nested data such as:
- restaurant details
- user details
- list of food items
- quantities
- total amount

This structure is well suited to a **document-oriented database**, where the full order can be stored as a single document.

This also illustrates one of the strengths of microservices:
each service can choose the persistence model that best fits its business domain.

---

### 5. Local Application Overview

In local development, the frontend runs independently from the backend services, but communicates with them through HTTP.

The typical local setup is:
- Angular frontend on `localhost:4200`
- Spring Boot microservices on ports such as `9091`, `9092`, `9093`, `9094`
- Eureka Server on `8761`

Because the frontend and backend do not run on the same port, **CORS configuration** is required on the backend side to allow Angular to consume the APIs during development.

Below is the high-level view of the application in local development:

![Local architecture](https://github.com/lhdenis/deployment-folder/blob/master/angular_springboot.png)

---

### 6. Cloud Deployment

The application is deployed on **AWS** using a cloud-native architecture.

Each backend service and the frontend are:
1. packaged as **Docker images**
2. pushed to a container registry
3. deployed in **Amazon EKS**, AWS’s managed Kubernetes service

Within Kubernetes:
- each application runs in one or more **pods**
- each application is exposed internally through a **Service**
- Kubernetes handles replica management and pod lifecycle

This makes the application:
- portable
- scalable
- resilient to pod restarts

Below is the high-level cloud deployment architecture:

![Cloud architecture](https://github.com/lhdenis/deployment-folder/blob/master/architecture_kubernetes.png)

---

### 7. Networking and Exposure

The application is exposed through an **AWS Load Balancer** configured with **Ingress**.

This provides:
- a single external entry point
- path-based routing
- internal traffic distribution to the correct service

Typical routing logic is:
- `/` → Angular frontend
- `/restaurant` → restaurant backend
- `/foodCatalogue` → food catalog backend
- `/order` → order backend
- `/user` → user backend

This means the user never accesses pods or internal services directly.
Instead, traffic flows through:

**Client → Load Balancer → Ingress → Kubernetes Service → Pod**

This approach improves:
- security
- maintainability
- scalability
- routing clarity

---

### 8. Code Quality and Testing

The project integrates **JUnit** unit tests and **SonarQube** quality analysis.

The goal is to ensure that:
- the code compiles correctly
- tests pass successfully
- code coverage remains acceptable
- code smells and maintainability issues are detected early

This is especially important before deploying changes automatically through CI/CD.

---

### 9. CI/CD Pipeline

The project uses a complete CI/CD workflow with **GitHub**, **Jenkins**, and **Argo CD**.

#### Continuous Integration – Jenkins
Jenkins runs on a dedicated **AWS EC2 instance**.

Its responsibilities include:
- cloning the source code repository
- running Maven build and test مراحل
- performing SonarQube analysis
- building Docker images
- pushing updated images
- updating Kubernetes deployment manifests with the new image tag

Jenkins is responsible for **validating and packaging** the application.

#### Continuous Deployment – Argo CD
Argo CD runs **inside the Kubernetes cluster**.

It continuously monitors the **deployment repository** containing the Kubernetes manifests.
When a manifest changes, Argo CD detects the desired new state and synchronizes the cluster automatically.

This means:
- updated images are redeployed automatically
- replica changes are applied automatically
- rollback is simplified by reverting the manifest state in Git

Argo CD is therefore responsible for **deploying and synchronizing** the Kubernetes environment.

---

### 10. End-to-End Functional Flow

From a user perspective, the application works as follows:

1. The user lands on the Angular frontend
2. The frontend retrieves the list of restaurants from `restaurant-listing-service`
3. The user selects a restaurant
4. The frontend requests the corresponding food catalog from `food-catalog-service`
5. The user adds food items to the cart
6. The frontend builds an order summary
7. The order is sent to `order-service`
8. `order-service` retrieves user details from `user-service`
9. The completed order is saved in MongoDB Atlas

This illustrates a full end-to-end flow across:
- frontend
- multiple microservices
- SQL and NoSQL databases
- cloud deployment infrastructure
