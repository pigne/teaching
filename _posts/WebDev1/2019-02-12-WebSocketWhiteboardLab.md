---
layout: post
title: Whiteboard WebSocket Lab
categories:
- WebDev1
- lab
author: Yoann Pigné
published: true
update: 2021-02-15
---

Faire une divergence (un *fork*) du projet <https://www-apps.univ-lehavre.fr/forge/2020-2021-m1/WEB-whiteboard-websocket-lab>


Un tableau blanc sur une Web app est une surface sur laquelle les utilisateurs peuvent dessiner. Chaque utilisateur a sa propre couleur et voit en temps réel les dessins des autres utilisateurs dans leur couleur respective.

Un utilisateur doit pouvoir créer un nouveau dessin vierge. Il doit aussi pouvoir  afficher la liste des dessins en cours et participer à ces dessins.


Techniquement le dessin se fait à l'aide d'un  [*canvas* html5](https://developer.mozilla.org/fr/docs/Web/Guide/Graphics/Dessiner_avec_canvas).

La base du code présente ici est celle de la 
[démo WebSocket](https://www-apps.univ-lehavre.fr/forge/pigne/WEB-websocket-demo). Il faut s'en inspirer 
pour  permettre l'interconnection et le partage des dessins de chacun.
On ne souhaite utiliser aucune autre technologie que les WebSocket et les Canvas supportés nativement par le navigateur. On n'utilisera ni framework ni bibliothèque de dessins.
 
Projet à rendre sous forme d'un *merge request* du projet initial sur la forge de l'université.


## Échéance

TP à rendre pour le : 

- Groupe A : 8 mars 2021
- Groupe B : 9 mars 2021
- Groupe C : 17 mars 2021
  
## Évaluation

[Liste des aptitudes évaluées.](/teaching/WebDev1#websocket)

