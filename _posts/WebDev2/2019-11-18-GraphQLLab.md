---
layout: post
title: GraphQL Lab
categories:
- WebDev2
- lab
author: Yoann Pigné
published:  true
update: 2023-11-05
---




On cherche à utiliser GraphQL comme système d'interrogation de notre base de données d'annonces immobilières.

En se basant sur la [documentation en ligne](https://graphql.org/learn) et sur le précédent projet (API REST) on va mettre en place un serveur GraphQL avec l'une des implémentations, dans un des [langages supportés](https://graphql.org/code/). De préférence on utilisera l'[implémentation de référence en JS](https://graphql.org/code/#javascript).

## Travail à effectuer

- En binôme
- Un projet git privé sur la forge qui héberge le code et les tests.
- Un serveur HTTP implémentant votre API avec Swagger et un point d'entrée GraphQL(de préférence avec Express) et connecté à une base de donnée (votre base  MongoDB d'annonces immobilières). Pour utiliser GraphQL avec express on préfèrera le projet [graphql-http](https://www.npmjs.com/package/graphql-http#with-express)
- Une validation des requêtes grâce au système de [validation](https://graphql.org/learn/validation/) de GraphQL.
- Une validation du service grâce à des tests d'intégration (des requêtes) permettant d'effectuer des requêtes paramétriques sur le serveur.


Il est convenu de m'envoyer un mail portant le titre "[M2 IWOCS WEB] Projet n°2" avec les noms des binômes et le lien vers le projet sur la forge. Ne pas oublier de me donner les droits suffisants.

## Échéance et Évaluation


- Ce travail doit être rendu pour le **dimanche 19 novembre 2023**.
- Évaluation à l'oral, en binômes, en salle machine, pendant 20 minutes.
- Présentation  de Swagger, GraphQL et du projet réalisé.
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
  - D1.C4 --  Maitriser l’écriture des tests et la couverture du code
- D2
  - D2.C2 --  Concevoir et réaliser des API et des protocoles de communication
  - D2.C3 --  Maitriser l'architecture logicielle d'un projet (fichiers, classes, composants, dépendances)
- D4
  - D4.C1 --  Maitriser un outil collaboratif de gestion de code (git)