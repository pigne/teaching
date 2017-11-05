---
layout: post
title: Lab React Router
categories:
- FullStackJS
- lab
author: Yoann Pigné
published: false
---


On veut écrire une nouvelle itération de notre Web app de manipulation IoT. On s'intéresse ici à l'arborescence de la Web App.

Voici le schéma de l'application :

![IoT Stecth]({{ site.baseurl }}/images/router_iot_app.svg)

En reprenant les idées de découpage de l'application "ReSpotify" Proposer une implémentation avec React et React Router de l'application.

On réutilisera les éléments déjà développés dans les labs précédents. (modèle objets, connecteur MQTT/WebSocket).

## Webpack modulaire

La configuration du compilateur de module  Webpack n'est pas facile. Le projet `Webpack Blocks` propose une approche modulaire pour configurer Webpack. On utilisera Webpack Block et son exemple de projet :

<https://github.com/andywer/webpack-blocks/tree/master/test-app>

## Flexbox

Pour la mise en page de l'application, on utilisera le mode de mise en page [`flexbox`](https://developer.mozilla.org/fr/docs/Web/CSS/Disposition_des_bo%C3%AEtes_flexibles_CSS/Utilisation_des_flexbox_en_CSS).
