---
layout: post
title: WebSocket Lab
categories:
- FullStackJS
- lab
author: Yoann Pigné
published: false
---

On veut réaliser une web app qui se connecte a un serveur MQTT, afin de recevoir des messages sur l'état de capteurs dans un réseau local.

Lire la doc sur [Le protocole MQTT](https://mosquitto.org/man/mqtt-7.html).


Les messages sont envoyés sur avec un topic du type:

```
value/[ID]
```

avec `[ID]` la valeur de l'identifient du senseur.

Les messages envoyées sont au format JSON et du type :

```JSON
{
     "value": "[value]",
     "type": "[SensorType]"
}
```

avec `[value]` la représentation en string de la valeur du senseur et `[sensorType]` le type de données parmi :

-  'POSITIVE_NUMBER',
-  'PERCENT',
-  'ON_OFF',
-  'OPEN_CLOSE'.

En reprenant la base du code de la
[démo WebSocket](https://github.com/ULH-WebDevelopment/WebSocketDemo) réaliser une Web app permettant de

- de se connecter à un serveur MQTT donnée,
- de souscrire a tous les messages de ce serveur,
- de créer des instances avec le model objet développé la semaine dernière,
- d'afficher simplement (à la manière de l'exemple sur les WebSocket) une trace de chaque message reçu.



Projet a rendre sous forme d'un projet GIT pour le 29 novembre 2016.


<!--
Un tableau blanc sur une Web app est une surface sur laquelle les utilisateurs peuvent dessiner. Chaque utilisateur a sa propre couleur. et voit les dessins des autres utilisateurs dans leur couleur respective.

Un utilisateur doit pouvoir créer un nouveau dessin vierge. Il doit aussi pouvoir  afficher la liste des dessins en cours et participer à ces dessins.


Techniquement le dessin se fait à l'aide d'un  [*canvas* html5](https://developer.mozilla.org/fr/docs/Web/Guide/Graphics/Dessiner_avec_canvas).

Une WebSocket permet de partager les dessins de chacun.

On ne souhaite utiliser aucune autre technologie que les WebSocket et les Canvas supportés nativement par le navigateur. On n'utilisera ni framework ni bibliothèque de dessins.
 -->
