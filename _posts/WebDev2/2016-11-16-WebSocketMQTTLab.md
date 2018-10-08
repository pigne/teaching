---
layout: post
title: WebSocket & MQTT Lab
categories:
- WebDev2
- lab
author: Yoann Pigné
published: true
update: 2018-10-07
---

On veut réaliser une web app qui se connecte a un serveur MQTT, afin de recevoir des messages sur l'état de capteurs dans un réseau local.

Lire la doc sur [Le protocole MQTT](https://mosquitto.org/man/mqtt-7.html).

Les messages sont envoyés avec un topic du type:

```
value/[ID]
```

avec `[ID]` la valeur de l'identifient du capteur.

Les messages envoyées sont au format JSON et du type :

```JSON
{
     "value": "[value]",
     "type": "[SensorType]"
}
```

avec `[value]` la représentation en string de la valeur du capteur et `[sensorType]` le type de données parmi :

-  'POSITIVE_NUMBER',
-  'PERCENT',
-  'ON_OFF',
-  'OPEN_CLOSE'.

En divergeant (fork) le projet de base 
[WebSocket MQTT lab](https://www-apps.univ-lehavre.fr/forge/WEB-IHM/websocket-mqtt-lab) réaliser une Web app permettant de :

- de se connecter à un serveur MQTT donnée,
- de souscrire a tous les messages de ce serveur,
- de créer des instances avec le model objet développé la semaine dernière,
- d'afficher une liste des capteurs qui se met à jours en fonction des données qui arrivent.
- en face de chaque capteur, on affiche la dernière valeur reçu, et quand cela est possible, on affiche également la valeur moyenne.

Projet à rendre sous forme d'un *merge request* à partir du projet de départ : <https://www-apps.univ-lehavre.fr/forge/WEB-IHM/websocket-mqtt-lab>

On pourra se servir du projet suivant pour générer des données de capteurs aléatoires : <https://github.com/pigne/random-sensors.git>

## Évaluation

[Liste des Capacités évaluées.](/teaching/WebDev2#ws-mqtt)

