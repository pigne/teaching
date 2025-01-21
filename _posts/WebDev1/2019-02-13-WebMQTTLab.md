---
layout: post
title: WebSocket & MQTT Lab
categories:
- WebDev1
- lab
author: Yoann Pigné
published: false
update: 2024-03-26
---

**Ce TP n'est pas a rendre. Il ne sera pas évalué.**

On veut réaliser une web app qui se connecte a un serveur MQTT, afin de recevoir des messages sur l'état de capteurs dans un réseau.

Lire la doc sur [Le protocole MQTT](https://mosquitto.org/man/mqtt-7.html).

Les messages sont envoyés avec un topic du type:

```
value/[ID]
```

avec `[ID]` la valeur de l'identifient du capteur.

Les messages envoyées sont au format JSON et du type :

```JSON
{
     "name": "[name]",
     "value": "[value]",
     "type": "[SensorType]"
}
```

avec `[name]` le nom du capteur (une chaîne de caractères),  `[value]` la représentation en string de la valeur du capteur et `[sensorType]` le type de données parmi :

-  'POSITIVE_NUMBER',
-  'PERCENT',
-  'ON_OFF',
-  'OPEN_CLOSE'.

 Réaliser une Web app avec des composants React permettant de :

- de se connecter à un serveur MQTT dont l'URL est donnée par l'utilisateur depuis un `input`
- de souscrire a tous les messages de ce serveur,
- d'afficher une liste des capteurs qui se met à jours en fonction des données qui arrivent
- en face de chaque capteur, on affiche la dernière valeur reçu (on ne sauvegarde pas d'historique)

<!-- Projet à rendre sous forme d'un *merge request* à partir du projet de départ : <https://www-apps.univ-lehavre.fr/forge/20120-2021-m1/WEB-mqtt-lab> -->

On pourra se servir du projet suivant pour générer des données de capteurs aléatoires : <https://github.com/pigne/random-sensors.git>

Une instance du générateur aléatoire tourne également ici  : 

- host : random.pigne.org
- port mqtt : 1883
- port websocket : 9001

## Évaluation

<!-- [Liste des aptitudes évaluées.](/teaching/WebDev1#websocket) -->
Ce TP n'est pas évalué. 


