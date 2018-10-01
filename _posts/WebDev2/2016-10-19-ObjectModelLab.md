---
layout: post
title: Object Model Lab
categories:
- WebDev2
- lab
author: Yoann Pigné
published: true
---

Dans le cadre de la conception d'une application de gestion de capteurs de type internet des objets (*Internet of Things*, IoT), on veux créer un modèle objet permettant de représenter des capteurs et les données qu'ils génèrent.

Le modèle objet est le suivant.

![Object Model]({{ site.baseurl }}/images/2016-M2-ObjectModelLab.svg)

Ce modèle n'est qu'une ébauche. Il est largement incomplet. Le but est de pouvoir créer des objets qui représentent des mesures prises par des capteurs et de pouvoir analyser ses données. Plus tard on souhaitera afficher ces données. Pour l'instant on veut simplement identifier le type de capteur et avoir des informations de bases (type de capteur, nombre de valeurs, valeur moyenne, date de dernière mesure, etc.)

On souhaite pouvoir créer de tels objets à partir d'un fichier de données JSON qui nous serait donné par un service tiers. Par exemple :

```JSON
[
  {
    "id": 1234,
    "name": "Température Bureau",
    "type": "TEMPERATURE",
    "data": {
      "values": [23,23,22,21,23,23,23,25,25],
      "labels": ["2016-10-19T08:00:00.000Z", "2016-10-19T09:00:00.000Z",
        "2016-10-19T10:00:00.000Z", "2016-10-19T11:00:00.000Z",
        "2016-10-19T12:00:00.000Z","2016-10-19T13:00:00.000Z",
        "2016-10-19T14:00:00.000Z","2016-10-19T15:00:00.000Z",
        "2016-10-19T16:00:00.000Z"
      ]
    }
  },
  {
    "id": 10245,
    "name": "Porte du Garage",
    "type": "DOOR",
    "data": {
      "value": 0
    }
  },
  {
    "id": 2222,
    "name": "Ventilateur Ordinateur Bureau",
    "type": "FAN_SPEED",
    "data": {
      "values": [1073,1800,2299,2176,1899,1400],
      "labels": ["2016-10-19T10:00:00.000Z", "2016-10-19T10:05:00.000Z",
        "2016-10-19T10:10:00.000Z", "2016-10-19T10:15:00.000Z",
        "2016-10-19T10:20:00.000Z","2016-10-19T10:25:00.000Z"
      ]
    }
  }
]
```

## Travail à réaliser

Comme pour le tp précédent, on va utiliser la forge de l'université. Forker et cloner le projet <https://www-apps.univ-lehavre.fr/forge/WEB-IHM/JSObjectModelLab.git>

En utilisant le pattern de création d'objets de votre choix (classique, `Object.create`, différentiel, fonctionnel ou `class`) créer la hiérarchie de classes permettant de représenter des données de capteur.

Rédiger des tests unitaires permettant de vérifier le bon fonctionnement du modèle. Le fichier `resources/sensors_data.js` sera utilisé pour générer les objets et vérifier certaines propriétés de base (qu'il vous appartient de définir).

Enfin un *merge request* permettra de rendre le TP. Penser a donner **vos nom et prénom** dans le message du PR.

## Evaluation

Liste des capacités évaluées :

- Savoir respecter les consignes d'un énoncé
- Savoir respecter une échéance
- Savoir écrire des tests et gérer la couverture du code
- Maîtriser un outil collaboratif de gestion de code (git)
- Maîtriser un modèle objet en JS