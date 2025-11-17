---
layout: post
title: Lab - Web Services
categories:
- WebDev2
- lab
author: Yoann Pigné
published: false
---


On souhaite transformer notre application de gestion d'annonces immobilières (réalisée au TP précédent) en un **WebService complet**, sécurisé et documenté, utilisable par :

* une application Web,
* une application mobile,
* ou tout autre client automatisé.

Votre mission est de concevoir **un service Web moderne**, en TypeScript, respectant les standards REST et GraphQL.

Le **framework est libre** (NestJS, Fastify, Express + Zod, Hono, Elysia…), mais le projet doit impérativement être :

* écrit en **TypeScript**,
* structuré,
* testé,
* sécurisé.

---

## Objectifs 

1. **Créer une API REST complète**, typée, valide, paginée, documentée en Swagger/OpenAPI.
2. **Proposer une API GraphQL équivalente**, avec schéma + résolveurs.
3. **Sécuriser l’ensemble** à l’aide d’un système d’authentification JWT.
4. **Gérer les droits** (roles : `agent`, `user`, etc.).
5. **Mettre en place des tests** :
   * tests unitaires (services),
   * tests End-to-End (API REST + GraphQL).
6. **Rendre le code propre, typé, organisé.**
7. **Mener un projet collaboratif structuré avec Git :**
  * création d’une hiérarchie de branches fonctionnelles,
  * organisation du développement (feature branches, PR, intégration progressive),
  * utilisation rigoureuse de la forge universitaire.

---

## Description fonctionnelle

Votre WebService doit gérer au minimum les entités suivantes :

### Annonce immobilière

* id
* titre
* description
* prix
* surface
* ville
* datePublication
* auteur (relation User)

### Utilisateur

* id
* email
* password (hashé)
* rôle : `admin` ou `user`
* liste d'annonces publiées

### (Optionnel mais conseillé)

* Questions / Réponses sur une annonce
* Photos
* Favoris

---

##  Partie 1 — Création de l’API REST (Contract-first ou Code-first au choix)

### 1.1. Définir le *contrat* de l’API

Deux approches sont possibles (à choisir) :

#### **A. Code-first**

* utiliser le système de décorateurs du framework choisi
  (ex : NestJS + `@nestjs/swagger`)
* générez automatiquement la spec OpenAPI

#### **B. Contract-first**

* écrire un fichier `swagger.yaml` ou `openapi.yaml`
* générer le code serveur (ou les types)
* importer ce contrat dans votre projet TS

---

### 1.2. Endpoints REST obligatoires

####  Annonces

| Méthode | Endpoint        | Rôle                          |
| ------- | --------------- | ----------------------------- |
| GET     | `/annonces`     | Liste paginée                 |
| GET     | `/annonces/:id` | Détails                       |
| POST    | `/annonces`     | Création (auth requise)       |
| PUT/PATCH     | `/annonces/:id` | Modification                  |
| DELETE  | `/annonces/:id` | Suppression (admin ou agent) |

####  Authentification

| Méthode | Endpoint       | Rôle                   |
| ------- | -------------- | ---------------------- |
| POST    | `/auth/signup` | Inscription            |
| POST    | `/auth/login`  | Génère un JWT          |
| GET     | `/auth/me`     | Infos du user connecté |

---

##  Partie 2 — Authentification & Tokens JWT

Vous devez mettre en place un système :

* de **login / signup**
* de **génération d’un JWT** contenant au minimum :

  * userId
  * rôle
  * date d’expiration
* de **vérification du token** sur les routes protégées
* de **gestion des rôles** :

  * un agent peut modifier ses propres annonces
  * un admin peut tout modifier / supprimer
  * un admin gère les utilisateurs
* [facultatif] la mise en place d'une délégation d'authentification `Oauth2` (Google, gitlab, etc.)

Exemples de requêtes :

```sh
curl -H "Authorization: Bearer <token>" \
     https://localhost:3000/annonces
```

---

##  Partie 3 — Documentation Swagger

Votre WebService doit exposer une page de documentation :

* `/docs` ou `/api-docs`
* utilisant **Swagger UI**
* avec tous les endpoints REST documentés :

  * paramètres
  * queries
  * réponses
  * erreurs (404, 401, 403…)
  * modèles (User, Annonce, etc.)

---

## Partie 4 — Endpoint GraphQL obligatoire

Vous devez proposer un schéma GraphQL **équivalent** aux routes REST avec des requêtes de consultation (`query`) de modification (`mutation`).


### Contraintes techniques

* Les résolveurs doivent éviter le **N+1 problem** (ex : DataLoader ou include).
* L’auth doit aussi fonctionner côté GraphQL (JWT dans `Authorization:`).

---

## Partie 5 — Tests unitaires & End-to-End

Vous devez écrire :

### Tests unitaires

Sur :

* services (annonces, users)
* validation
* règles métier (ex : un agent ne peut modifier que ses annonces)

###  Tests E2E

Sur :

* login
* accès aux endpoints REST protégés
* création/modification/suppression d’annonces
* requêtes GraphQL

Outils conseillés :

* Jest
* Supertest (REST)
* GraphQL-request (GraphQL)

---

## Partie 6 — Livrables

Ce projet est à effectuer en **binôme**.  Dès le début du TP, vous devez m'envoyer un email avec objet `"[M2 IWOCS WEB] Projet n°2"` contenant les détails de votre projet (noms, prénoms, numéro étuidants et URL sur la forge). Le projet est hébergé sur la forge dans un **nouveau** projet, privé, auquel je suis invité avec les droits `developer`.

Votre projet forge doit contenir:
1. Le code complet structuré
2. Le fichier `swagger.yaml` (si contract-first)
3. Une page `/docs` fonctionnelle
4. Les scripts npm suivants :

   * `npm run start`
   * `npm run build`
   * `npm run test`
   * `npm run test:e2e`
5. Un README clair expliquant :

   * comment lancer le service
   * comment tester l’API
   * où trouver la documentation
---

##  Évaluation



Le projet doit être rendu avant le **13 décembre 2025**. Il servira de base à l'évaluation du lundi 15 décembre.  Les détails de l'implémentation et le fonctionnement du projet seront présentés et discutés.

Les compétences seront validées à la fois sur la base du rendu du projet et de l'évaluation orale.

Ce travail doit être réalisé en **binôme**. Chaque membre du binôme doit être capable de présenter le projet et de répondre aux questions.

[**Liste des aptitudes évaluées**](/teaching/WebDev2/#evaluations-et-aptitudes) (colonne « Web Services »)


