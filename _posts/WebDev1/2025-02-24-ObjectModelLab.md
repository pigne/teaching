---
layout: post
title: TP Modélisation Objet
categories:
- WebDev1
- lab
author: Yoann Pigné
published: true
---



Dans le cadre du développement d'une application de gestion de flotte de véhicules connectés, nous allons concevoir un modèle objet permettant de représenter les différents types de véhicules, leurs capteurs embarqués et les données qu'ils génèrent.

## Objectif

L'objectif est de modéliser la hiérarchie des véhicules et capteurs, puis de tester la cohérence du modèle avec des tests unitaires. On souhaite pouvoir charger des objets véhicules à partir de données JSON et analyser leurs mesures.

Les véhicules peuvent être de différents types, comme des voitures, des camions ou des vélos. Chaque véhicule peut être équipé de divers capteurs, par exemple un capteur de vitesse, un GPS ou un capteur de pression des pneus. Ces capteurs permettent de collecter des données en temps réel sur l'état et l'utilisation du véhicule. Un historique des valeurs collectées doit être conservé pour permettre des analyses ultérieures.

Votre mission est de :

- Concevoir la hiérarchie des classes représentant ces véhicules et capteurs.
- Implémenter des méthodes permettant de récupérer et d'exploiter les données des capteurs.
- Ajouter un historique des valeurs de chaque capteur pour suivre l'évolution des données.
- Vérifier le bon fonctionnement de votre modèle en rédigeant des tests unitaires avec une couverture de code suffisante.

## Modèle Objet (UML)

![Object Model]({{ site.baseurl }}/images/2025-M1-ObjectModelLab.jpg)


<!-- 
```mermaid
classDiagram
    class Vehicle {
    -id: number
    -brand: string
    -model: string
    -year: number
    -sensors: Sensor[]
    +getSensor(type: string): Sensor | null
    }

    class Car {
    -fuelType: string
    +getFuelLevel(): number
    }
    class ElectricCar {
    +batteryCapacity: number
    +getBatteryStatus(): number
    }

    class Truck {
    +maxLoad: number
    +getCurrentLoad(): number
    }

    class Bike {
    +type: string
    }

    class Sensor {
    +id: number
    +type: string
    +history: SensorHistory[]
    +getData(): SensorHistory | null
    }

    class SensorValue {
    +value: number | Position
    }

    class SensorHistory {
        +timestamp: string
        +value: SensorValue
    }

SensorHistory -- SensorValue

    class Position {
    +lat: number
    +lon: number
    }

    class GPSSensor {
    +getLocation(): Position
    }

    class SpeedSensor {
    +getSpeed(): number
    +getAverageSpeed(): number
    }
    
    class FuelLevelSensor {
        +getFuelLevel(): number
    }

    Vehicle <|-- Car
    Vehicle <|-- ElectricCar
    Vehicle <|-- Truck
    Vehicle <|-- Bike
    Sensor <|-- GPSSensor
    Sensor <|-- SpeedSensor
    Sensor <|-- FuelLevelSensor
    Sensor o-- SensorHistory
    Vehicle o-- Sensor
``` -->

## Données JSON de test

Voici un exemple de fichier JSON représentant une flotte de véhicules connectés avec historique des capteurs :

```json
[
  {
    "id": 2,
    "brand": "Volvo",
    "model": "FH16",
    "year": 2020,
    "type": "Truck",
    "maxLoad": 40000,
    "sensors": [
      {
        "id": 103,
        "type": "GPS",
        "history": [
          { "timestamp": "2025-02-10T12:00:00Z", "value": {"lat": 45.763, "lon": 4.8355} },
          { "timestamp": "2025-02-10T12:05:00Z", "value": {"lat": 45.764, "lon": 4.8357} }
        ]
      },
      {
        "id": 104,
        "type": "Speed",
        "history": [
          { "timestamp": "2025-02-10T11:50:00Z", "value": 72 },
          { "timestamp": "2025-02-10T12:00:00Z", "value": 75 }
        ]
      }
    ]
  }
]
```

## Travail à réaliser

1. *Forker* et cloner le projet [https://www-apps.univ-lehavre.fr/forge/2024-2025-m1/WEB-objectmodel-lab.git](https://www-apps.univ-lehavre.fr/forge/2024-2025-m1/WEB-objectmodel-lab.git).
2. Rendre le projet privé et m'ajouter en tant que développeur.
3. Construire la hiérarchie de classes TypeScript permettant de représenter les véhicules et les capteurs. Le diagramme UML fourni peut être amélioré ou modifié pour mieux correspondre à votre conception. Justifiez vos choix de conception.
4. Implémenter la logique permettant de charger les véhicules à partir du JSON et de récupérer les données de leurs capteurs.
5. Ajouter la gestion de l'historique des valeurs des capteurs (mise à jour, récupération des valeurs passées, calculs statistiques sur l'évolution des données).
6. Rédiger des tests unitaires couvrant au moins 90% du code. Ces tests doivent inclure :
   - Vérification du bon chargement des objets à partir du JSON.
   - Vérification du stockage et de la récupération de l'historique des capteurs.
   - Calcul et validation des valeurs renvoyées par les capteurs (ex. vitesse moyenne, position GPS valide, niveau de batterie).
   - Gestion des erreurs et des cas limites (données manquantes, valeurs incorrectes, etc.).
7. Documenter votre code et expliquer les choix de conception dans un fichier `README.md`.

## Échéance

TP à rendre sous forme d'un Merge Request pour le : 11/03/2025

## Évaluation

[Liste des aptitudes évaluées.](/teaching/WebDev1#object-models)

