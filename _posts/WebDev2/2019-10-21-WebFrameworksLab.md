---
layout: post
title: Lab - Frameworks coté Serveur
categories:
- WebDev2
- lab
author: Yoann Pigné
published: true
---

On souhaite réaliser un site Web classique CRUD de gestion d'annonces immobilières.

## User Story

 Un site Web de gestion d'annonces immobilières contient et affiche des annonces immobilières. Un utilisateur non connecté doit pouvoir consulter les annonces. Un utilisateur connecté doit pouvoir en plus, poser des questions sur une annonce qu'il consulte. Un agent immobilier doit pouvoir ajouter, modifier, supprimer une annonce. Il peut aussi répondre aux questions des utilisateurs sur les annonces affichées.

Une annonce immobilière est composée : 

- d'un titre, 
- d'un type de bien (à la vente ou à la location), 
- d'un status de publication (publiée, non publiée),
- d'un status de transaction (disponible, louée, vendue),
- d'une description longue, 
- d'un prix (de vente ou de location),
- d'une date de disponibilité,
- de photos (éventuellement),
- de questions posées par des utilisateurs, 
- de réponses au questions répondues par les agents immobiliers. 

Lors de la création d'une annonce, un agent doit pouvoir ajouter (glisser déposer) autant de photos que voulu.

## But de ce TP

Le but est de mettre en application les compétences déjà acquises en licence et M1. Ce TP ne fait pas  vraiment appel de nouveaux paradigmes de programmation. En revanche  deux technologies sont imposées.

Le framework Express pour node.js doit être utilisé pour gérer :

- la gestion de sessions, les cookies,
- l' authentification,
- la sécurité,
- le routage des pages Web,
- la gestion des formulaires, 
- la consultation et l'enregistrement en bases de données,
- La génération des pages à partir de templates,
- le logging,

Les données (annonces, utilisateurs, sessions, ...) sont stockées dans une base de données MongoDB.

On ne négligera pas non plus les aspects visuels. Les pages Web générées à partir des templates respecteront les standards Web (HTML et CSS) et proposeront une expérience utilisateur (UX) de qualité. Le site doit être [*responsive*](https://en.wikipedia.org/wiki/Responsive_web_design), la navigation doit atteindre un niveau d'[accessibilité minimal](https://fr.wikipedia.org/wiki/Accessibilit%C3%A9_du_web), les formulaires [efficaces](https://uxplanet.org/designing-more-efficient-forms-structure-inputs-labels-and-actions-e3a47007114f).

Une telle application doit être testée ! Au delà des tests unitaires comment tester la génération de pages web à partir du contenu d'une bado ?


## Travail avec la forge de l'université

Ce travail doit faire l'objet d'un projet GIT sur la forge. Il faut m'envoyer dès le début du TP  un mail (yoann.pigne@univ-lehavre.fr) avec entête "[M2 IWOCS WEB] Projet n°1". Ce mail m'indiquera votre nom, prénom, n° d'étudiant, login, et l'url de votre projet sur la forge.

## Évaluation

Ce travail doit être rendu pour le 3 novembre (délai de rigueur). Il ne sera pas corrigé mais servira de base à un oral, le 5 novembre, ou les détail d'implémentation et fonctionnement du projet seront présentés et expliqué. 

Les compétences seront validées sur la base du rendu du projet ainsi que sur cette évaluation orale.

Ce travail peut être fait en binôme. Dans ce cas les deux membres du binome doivent être capables de présenter le projet.


