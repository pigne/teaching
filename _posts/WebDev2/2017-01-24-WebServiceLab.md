---
layout: post
title: Lab Services Web
categories:
- WebDev2
- lab
author: Yoann Pigné
published: false
---


On souhaite apporter plusieurs fonctionnalités à notre application Web IoT grâce l'apport d'un service Web.

## Les fonctionnalités

### Fonctionnalité n°1

On veut pouvoir explorer l'historique des mesures prises par les capteurs. Pour cela on va mettre en place un service web (sous forme d'une API) qui va définir des requêtes d'accès pour des capteurs dans des intervalles données. Ainsi, en mettant en place un mécanisme de pagination le service va permettre de retrouver des plages de données pour un capteur donné.

On doit donc pouvoir interroger le service Web pour un capteur donnée entre 2 dates également données et recevoir en réponse toutes les mesures enregistrées concernant ce capteur entre ces 2 dates.

Visuellement on veut pouvoir choisir l'intervalle d'observation (une heure, une journée, une semaine) puis on doit avoir accès à des liens de pagination pour permettre de remonter dans le passer (aujourd'hui, hier, il y a 2 jours, etc.).

### Fonctionnalité n°2

On souhaite également pouvoir ajouter des informations concernant les capteurs. En particulier, l'application Web doit permettre l'ajout d'un nom (en plus de son identifiant) pour un capteur, ainsi qu'une localisation (sous forme d'une simple chaîne) permettant de situer le capteur (e.g. Bureau, chambre du p'tit, Amphi Lessueur, etc.)

## Points Techniques

### Acquisition des données

La captation des données vous est fournie. Elle est assurée par le projet suivant : <https://github.com/pigne/sensors-to-db>. Ce script attend 2 paramètres :

- l'URL d'un Broker MQTT,
- l'URL d'une base de données.

### stockage des données

Le stockage des données doit être assuré par une base de données MongoDB. D'ailleurs le projet `sensors-to-db` attend l'URL d'une db Mongo pour fonctionner. Deux Modèles sont nécessaires. Un pour stoker les informations relatives à un capteur et un pour les données (mesures) relevées par le capteur. Les schémas de données sont les suivants :

- `Sensor`

  ```js
  {
    _id: String,  // surcharge du champ '_id'
                  // ID du capteur (e.g. 'TEMPERATURE_BUREAU')
    name: String,
    location: String,
    type: String
  }
  ```

  - `Measure`

    ```js
    {
      // pas de '_id' car on laisse le mécanisme de clé primaire par défaut
      sensor_id: String, // ID du capteur (e.g. 'TEMPERATURE_BUREAU')
      date: {
        type: Date,
        default: Date.now
      },
      value: String
    }
    ```

### réalisation du service Web

On doit réaliser un service REST. On utilise [SWAGGER](http://editor.swagger.io/#/) pour concevoir l'API et pour générer le serveur et le client.

Le serveur pourra être connecté au serveur de DEV de webpack utilisé dans le template `create-react-app`. Voire [Proxying API Requests in Development](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#proxying-api-requests-in-development)

Le client doit faire partie d'une manière ou d'une autre des dépendances de notre site web. Il y a plusieurs solutions :

- Publier le client de l'API Web auto-généré par *Swagger* sur NPM, puis faire un `npm i -S mon_service_web` dans votre appli Web.
- Copié/collé le code du client dans l'appli web.
- Faire un [sous-module git](https://git-scm.com/book/fr/v2/Utilitaires-Git-Sous-modules).


## Rendu

L'appli complète avec un minimum de documentation pour pouvoir tout faire fonctionner est attendu pour avant-hier, délai de rigueur.

Toute tentative d'empaquetage sous forme de conteneurs (docker, docker-compose) sera la bienvenue.
