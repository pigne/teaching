---
layout: post
title: Lab React et React Router
categories:
- WebDev1
- lab
author: Yoann Pigné
published: false
update: 2022-03-22
---

On veut réaliser une web app qui se connecte à une source de données (un Web Service) afin de recevoir des informations  sur l'état de capteurs dans un réseau.

On s'intéresse ici à l'affichage et à la représentation graphique des données de capteurs.

Voici une idée générale de la structure de l'application :

![IoT Sketch]({{ site.baseurl }}/images/router_iot_app.svg)

En reprenant l'idée du [*Lifting State Up*](https://reactjs.org/tutorial/tutorial.html#lifting-state-up)  dans la démo de React, Proposer une implémentation avec ***React*** et ***React Router*** de l'application qui stocke son état dans un composant globale et délègue l'affichage des différents éléments de la page à des composants *React* imbriqués.

On souhaite avoir des URLs du type : 

- `/` 
- `/temp_bureau`
- `/ventilateur`
- `/temperature_salle_A111`
- ...

Ces routes sont dynamiquement créées à partir des données de capteurs récupérées dans la ressource indiquée dans l'`input`précédent. 

<!-- ## Broker MQTT

Un broker MQTT avec de faux capteurs est disponible à l'adresse : `random.pigne.org` sur le port `1883` pour les sockets réseaux classiques et sur le port `9001` pour le support WebSocket. -->

## Source de données 

En théorie on devrait connecter cette WebApp à un WebService. Ici, pour simplifier, on se contente de donner l'url d'une ressource sous forme de fichier JSON dans l'`input` URL du composant en haut de page. Cette ressource correspond au nom d'un fichier que l'on dispose dans le serveur, dans le dossier `public`. Dès que le champ `input` est modifié, la nouvelle resource est téléchargé de façon asynchrone pour actualiser la liste des capteurs. 

On imagine que le format des fichier JSON correspond au fichier de test du TP précédent. 

## *Flexbox* ou *CSS Grid Layout*

Pour la mise en page de l'application, on utilisera au choix le mode de mise en page [`flexbox`](https://developer.mozilla.org/fr/docs/Web/CSS/Disposition_des_bo%C3%AEtes_flexibles_CSS/Utilisation_des_flexbox_en_CSS) ou ['CSS grid layouts'](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout), ou les deux.

## CSS modules

On utilise les [CSS modules](https://github.com/css-modules/css-modules) pour gérer les feuilles de style. 

On veillera a bien nommer les fichier comme décrit dans la [documentation de `create-react-app`](https://facebook.github.io/create-react-app/docs/adding-a-css-modules-stylesheet).

## Tests

Les composants React **doivent** être testés comme n'importe quel code javascript. Se référer à l'[aide en ligne de React  sur les tests](https://fr.reactjs.org/docs/testing.html) pour tester le code. 

## Rendu

Comme pour les autres TP, on va forker un projet de base : <https://www-apps.univ-lehavre.fr/forge/2021-2022-m1/WEB-react-router-lab> et en proposer un *Merge Request* une fois le travail terminé. On n'oubliera pas de modifier le fichier `README.md` et de nommer correctement le *Merge Request* avec vos **nom**, **prénom** et **numéro d'étudiant**.

**Ce TP peut être fait en binôme.**


## Échéance

- 8 avril 2022

## Évaluation

[Liste des aptitudes évaluées.](/teaching/WebDev1#react--reactrouter--redux)

