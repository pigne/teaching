---
layout: post
title: Object Model Lab
categories:
- WebDev1
- lab
author: Yoann Pigné
published: false
update: 2023-02-07
---

Dans le cadre de la conception d'une application de gestion de capteurs de type internet des objets (*Internet of Things*, IoT), on veux créer un modèle objet permettant de représenter des capteurs et les données qu'ils génèrent.

Le modèle objet est le suivant.

![Object Model]({{ site.baseurl }}/images/2016-M2-ObjectModelLab.svg)

**Ce modèle n'est qu'une ébauche. Il est largement incomplet.** Le but est de pouvoir créer des objets qui représentent des mesures prises par des capteurs et de pouvoir analyser ses données. Plus tard on souhaitera afficher ces données. Pour l'instant on veut simplement identifier le type de capteur et avoir des informations de bases (type de capteur, nombre de valeurs, valeur moyenne, date de dernière mesure, etc.)

On souhaite pouvoir créer de tels objets à partir d'un fichier de données JSON qui nous serait donné par un service tiers. Par exemple :

```JSON
[
  {
    "id": 1234,
    "name": "Température Bureau",
    "type": "TEMPERATURE",
    "data": {
      "values": [23,23,22,21,23,23,23,25,25],
      "labels": ["2022-10-19T08:00:00.000Z", "2022-10-19T09:00:00.000Z",
        "2022-10-19T10:00:00.000Z", "2022-10-19T11:00:00.000Z",
        "2022-10-19T12:00:00.000Z","2022-10-19T13:00:00.000Z",
        "2022-10-19T14:00:00.000Z","2022-10-19T15:00:00.000Z",
        "2022-10-19T16:00:00.000Z"
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
      "labels": ["2022-10-19T10:00:00.000Z", "2022-10-19T10:05:00.000Z",
        "2022-10-19T10:10:00.000Z", "2022-10-19T10:15:00.000Z",
        "2022-10-19T10:20:00.000Z","2022-10-19T10:25:00.000Z"
      ]
    }
  }
]
```

## Travail à réaliser

- *Forker* (diverger) et cloner le projet <https://www-apps.univ-lehavre.fr/forge/2022-2023-m1/WEB-objectmodel-lab.git>.
- S'assurer que votre projet est bien privé.
- M'ajouter en tant que développeur à votre projet.
- M'envoyer un mail avec le titre `" [M1-WEB] TP n°2 "` avec vos **nom**, **prénom** et **URL de projet**. 
- Faire des commits régulier avec des messages claires. 
- En utilisant le pattern de création d'objets de votre choix (classique, `Object.create`, différentiel, fonctionnel ou `class`) créer la hiérarchie de classes permettant de représenter des données de capteur.
- Rédiger des tests unitaires permettant de vérifier le bon fonctionnement du modèle. Le fichier `resources/sensors_data.json` sera utilisé pour générer les objets et vérifier certaines propriétés de base (qu'il vous appartient de définir).

<!-- Enfin un *merge request* permettra de rendre le TP. Penser à donner **vos nom et prénom** dans le message du *merge request*. -->


## Échéance

TP à rendre pour le : 14/03/2023

## Évaluation

[Liste des aptitudes évaluées.](/teaching/WebDev1#object-models)