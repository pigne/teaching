---
layout: post
title: Lab - Frameworks coté Serveur
categories:
- WebDev2
- lab
author: Yoann Pigné
published: true
update: 2021-10-24
---

Le but de ce TP est une mise en application des compétences déjà acquises dans le cursus (Licence, M1) avec la contrainte d'utiliser de nouvelles technologies. On propose de réaliser un site Web classique CRUD de gestion d'annonces immobilières.

## User Story

Un site Web de gestion d'annonces immobilières contient et affiche des annonces immobilières. Un utilisateur non connecté doit pouvoir consulter les annonces. Un utilisateur connecté doit pouvoir en plus, poser des questions sur une annonce qu'il consulte. Un agent immobilier doit pouvoir ajouter, modifier, supprimer une annonce. Il peut aussi répondre aux questions des utilisateurs sur les annonces affichées.

Une annonce immobilière est composée :

- d'un titre,
- d'un type de bien (à la vente ou à la location),
- d'un status de publication (publiée, non publiée),
- d'un status de bien (disponible, loué, vendu),
- d'une description longue,
- d'un prix (de vente ou de location),
- d'une date de disponibilité,
- de photos (éventuellement),
- de questions posées par des utilisateurs,
- de réponses aux questions, répondues par les agents immobiliers.

Lors de la création d'une annonce, un agent doit pouvoir ajouter (**glisser déposer**) autant de photos que voulu.

## But de ce TP

Le but est de mettre en application les compétences déjà acquises en Licence et M1. Ce TP ne fait pas appel de nouveaux paradigmes de programmation. En revanche deux technologies sont imposées.

Le framework *Express* pour *node.js* doit être utilisé pour gérer :

- le routage des pages Web,
- la gestion des formulaires,
- la consultation et l'enregistrement en bases de données,
- La génération des pages Web à partir de templates,
- le logging,
- les sessions,
- les cookies,
- l'authentification,
- la sécurité (des routes),


Les données (annonces, utilisateurs, sessions, ...) sont stockées dans une base de données *MongoDB*.

On ne négligera pas non plus les aspects visuels. Les pages Web générées à partir des templates respecteront les standards Web (HTML et CSS) et proposeront une expérience utilisateur (UX) de qualité. Le site doit être [*responsive*](https://en.wikipedia.org/wiki/Responsive_web_design), la navigation doit atteindre un niveau d'[accessibilité minimal](https://fr.wikipedia.org/wiki/Accessibilit%C3%A9_du_web), les formulaires [efficaces](https://uxplanet.org/designing-more-efficient-forms-structure-inputs-labels-and-actions-e3a47007114f).

Une telle application doit être **testée** ! Au-delà des tests unitaires comment tester la génération de pages web à partir du contenu d'une base de données ? Il vous appartient de répondre à cette question en **proposant** une solution technique.

## Travail avec la forge de l'université

Ce travail doit faire l'objet d'un projet sur la [forge de l'université](https://www-apps.univ-lehavre.fr/forge/). Il faut m'envoyer dès le début du TP un email (<yoann.pigne@univ-lehavre.fr>) avec entête "[M2 IWOCS WEB] Projet n°1". Ce mail m'indiquera : votre nom, prénom, n° d'étudiant, login et l'URL de votre projet sur la forge.

N'oubliez pas de me donner accès au projet en m'ajoutant en tant que "`developer`".

## Évaluation

Ce travail doit être rendu pour le 14 octobre 2022 (délai de rigueur). Il ne sera pas corrigé, mais servira de base à un oral, le 18 octobre 2022, où les détails d'implémentation et le fonctionnement du projet seront présentés et expliqués.

Les compétences seront validées sur la base du rendu du projet ainsi que sur cette évaluation orale.

Ce travail est à faire en binôme. Les deux membres du binôme doivent être capables de présenter le projet et de répondre aux questions.