---
layout: post
title: Lab - Frameworks coté Serveur
categories:
- WebDev2
- lab
author: Yoann Pigné
published: true
update: 2024-09-09
---


# Objectif du TP

L'objectif de ce TP est de mettre en pratique les compétences acquises au cours du cursus (Licence, M1) avec la contrainte d'utiliser des technologies nouvelles. Il s'agit de développer un site web classique de gestion d'annonces immobilières avec des fonctionnalités CRUD.

## User Story

Un site de gestion d'annonces immobilières permet d'afficher et de gérer ces annonces. Voici les rôles et fonctionnalités :

- Un utilisateur non connecté peut consulter les annonces.
- Un utilisateur connecté peut poser des questions sur une annonce qu'il consulte.
- Un agent immobilier peut ajouter, modifier, ou supprimer une annonce. Il peut également répondre aux questions des utilisateurs concernant une annonce.

Une annonce immobilière comprend les éléments suivants :

- un titre,
- un type de bien (vente ou location),
- un statut de publication (publiée ou non publiée),
- un statut du bien (disponible, loué, vendu),
- une description détaillée,
- un prix (de vente ou de location),
- une date de disponibilité,
- des photos (facultatives),
- des questions posées par les utilisateurs,
- des réponses aux questions, fournies par les agents immobiliers.

Lors de la création d'une annonce, un agent doit pouvoir ajouter autant de photos qu'il le souhaite via un système de **glisser-déposer**.

## Objectif pédagogique

L'objectif de ce TP est d'appliquer les compétences techniques acquises en Licence et M1. Ce projet n'introduit pas de nouveaux paradigmes de programmation, mais impose l'utilisation de technologies modernes et adaptées aux pratiques actuelles.

### Technologies requises

- **TypeScript** : Le développement de ce projet doit être réalisé en utilisant TypeScript pour améliorer la robustesse du code et bénéficier de la vérification statique des types.
  
- **Express** pour *Node.js* sera utilisé pour :
  - le routage des pages web,
  - la gestion des formulaires,
  - la consultation et l'enregistrement dans la base de données,
  - la génération des pages web à partir de templates,
  - le logging,
  - la gestion des sessions,
  - les cookies,
  - l'authentification,
  - la sécurisation des routes.

Les données (annonces, utilisateurs, sessions, etc.) seront stockées dans une base de données *MongoDB*.

L'aspect visuel du site ne devra pas être négligé. Les pages web générées à partir des templates devront respecter les standards web (HTML et CSS) et offrir une expérience utilisateur (UX) de qualité. Le site devra être [*responsive*](https://en.wikipedia.org/wiki/Responsive_web_design), assurer un minimum d'[accessibilité](https://fr.wikipedia.org/wiki/Accessibilit%C3%A9_du_web), et proposer des [formulaires efficaces](https://uxplanet.org/designing-more-efficient-forms-structure-inputs-labels-and-actions-e3a47007114f).

## Tests

Une application de ce type doit être **testée** ! En plus des tests unitaires, une réflexion est attendue sur la manière de tester la génération des pages web à partir des données en base de données. Vous devrez proposer une solution technique adaptée.

## Travail sur la forge universitaire

Le projet doit être hébergé sur la [forge de l'université](https://www-apps.univ-lehavre.fr/forge/). Dès le début du TP, vous devez m'envoyer un email à l'adresse <yoann.pigne@univ-lehavre.fr> avec comme objet `"[M2 IWOCS WEB] Projet n°1"`. Ce message devra inclure :

- votre nom et prénom,
- votre numéro d'étudiant,
- votre login,
- et l'URL du projet sur la forge.

N'oubliez pas de m'accorder l'accès au projet en m'ajoutant en tant que `developer`.

## Évaluation

Le projet doit être rendu avant le **6 octobre 2024** (délai impératif). Il ne sera pas corrigé, mais servira de base à un oral les **7 et 8 octobre 2024**. Lors de cet oral, les détails de l'implémentation et le fonctionnement du projet seront présentés et discutés.

Les compétences seront validées à la fois sur la base du rendu du projet et de l'évaluation orale.

Ce travail doit être réalisé en **binôme**. Chaque membre du binôme doit être capable de présenter le projet et de répondre aux questions.
