---
layout: post
title: Whiteboard WebSocket Lab
categories:
- WebDev1
- lab
author: Yoann Pigné
published: true
update: 2024-03-11
---


Un tableau blanc sur une Web App est une surface sur laquelle les utilisateurs peuvent dessiner. Chaque utilisateur a sa propre **couleur** et voit en **temps réel** les autres utilisateurs dessiner avec leur couleur respective.

Un utilisateur doit pouvoir **créer** un nouveau dessin vierge. Il doit aussi pouvoir  **afficher** la liste des dessins en cours et **participer** à ces dessins.


Techniquement le dessin se fait à l'aide d'un  [*canvas* html5](https://developer.mozilla.org/fr/docs/Web/Guide/Graphics/Dessiner_avec_canvas). 


On s'inspire de la base de code proposée dans la démo *websocket* du cours (<https://www-apps.univ-lehavre.fr/forge/pigne/WEB-websocket-demo>
). Il faut s'en inspirer  pour  permettre l'interconnection et le partage des dessins de chacun.
On ne souhaite utiliser aucune autre technologie que les WebSocket et les Canvas supportés nativement par le navigateur. On n'utilisera ni framework ni bibliothèque de dessins.
 
Penser à ce qui se passe quand un utilisateur se connecte à un dessin déjà commencé. Il peut recevoir les nouvelles modifications, mais voit-il les anciennes (celles qui précèdent sa connexion) ? Proposer une solution. 


## Travail à réaliser

- Travail à réaliser en **binome**
- On pourra au choix, copier le code de la démo [websocket](https://www-apps.univ-lehavre.fr/forge/pigne/WEB-websocket-demo) dans un nouveau projet vierge, ou bien faire une divergence pour obtenir une copie du code.
- S'assurer que votre projet est bien privé.
- M'ajouter en tant que développeur à votre projet.
- M'envoyer un mail avec le titre `" [M1-WEB] TP n°3 "` avec les  **nom** et  **prénom** des membres du binôme ainsi que l'**URL du projet**. 
- Faire des commits régulier avec des messages claires et réaliser le travail décrit au dessus. 


## Échéance

TP à rendre pour le :26/03/2024

## Évaluation

[Liste des aptitudes évaluées.](/teaching/WebDev1#websocket)

