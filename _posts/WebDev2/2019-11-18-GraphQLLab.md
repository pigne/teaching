---
layout: post
title: GraphQL Lab
categories:
- WebDev2
- lab
author: Yoann Pigné
published: true
---




On cherche à utiliser GraphQL comme système d'interrogation de notre base de données d'annonces immobilières.

En se basant sur la [documentation en ligne](https://graphql.org/learn) et sur le précédent projet (API REST) on va mettre en place un serveur GraphQL avec l'une des implémentations, dans un des [langages supportés](https://graphql.org/code/). De préférence on utilisera l'[implémentation de référence en JS](https://graphql.org/code/#javascript).

## Travail à effectuer

- En binome
- Un projet git sur la forge qui heberge le code et les tests.
- Un [sereur HTTP implementnat GraphQL](https://graphql.org/learn/serving-over-http/) (de préférence avec Express) et connecté à une base de donnée (votre base  MongoDB d'annonces immobilières).
- Une validation des requêtes grâce au système de [validation](https://graphql.org/learn/validation/) de GraphQL.
- Une validation du service grâce à des tests d'intégration (des requêtes)  permettant d'effectuer des requêtes paramétriques sur le serveur.


Il est convenu de m'envoyer un mail avec les noms des binômes et le lien vers le projet sur la forge. Ne pas oublier de me donner les droits suffisants.

## Échéance et Évaluation


- Le **3 décembre 2019**,  évaluation à l'oral, en binômes, en salle machine, pendant 20 minutes.
- Présentaion  de GraphQL et du projet réalisé.
- On doit pouvoir présenter et définir rapidement les concepts GraphQL suivants :
  - Variables
  - Fragments
  - Directives
  - Types (Query, Mutation, Scalar, Enumeration, Interface, Union, Input)
  - Inline Fragments
  - Meta fields
  - Lists
- On doit comprendre les notions de **validation** et d'**introspection** au sens de GraphQL.

## Aptitudes évaluées


- D1
  - D1.C4 --  Maîtriser l’écriture des tests et la couverture du code
- D2
  - D2.C2 --  Concevoir et réaliser des API et des protocoles de communication
  - D2.C3 --  Maîtriser l'architecture logicielle d'un projet (fichiers, classes, composants, dépendances)
- D4
  - D4.C1 --  Maîtriser un outil collaboratif de gestion de code (git)