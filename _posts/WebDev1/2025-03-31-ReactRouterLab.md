---
layout: post
title: Lab React et React Router
categories:
- WebDev1
- lab
author: Yoann Pigné
published: true

---

On veut réaliser une web app qui se connecte à une source de données (un Web Service) afin de recevoir des informations sur l'état des capteurs dans un réseau.

On s'intéresse ici à l'affichage et à la représentation graphique des données des capteurs.

Voici une idée générale de la structure de l'application :

![IoT Sketch]({{ site.baseurl }}/images/apercu_sensor_app.jpg)

On souhaite avoir des URLs du type :

- `/`
- `/temp_bureau`
- `/ventilateur`
- `/temperature_salle_A111`
- ...

Ces routes sont dynamiquement créées par ***React Router*** à partir des données des capteurs récupérées dans la ressource indiquée dans l'`input` précédent.

## Broker MQTT

Lire la doc sur [Le protocole MQTT](https://mosquitto.org/man/mqtt-7.html).

Les messages sont envoyés avec un topic du type :

```
value/[ID]
```

avec `[ID]` la valeur de l'identifiant du capteur.

Les messages envoyés sont au format JSON et du type :

```JSON
{
     "name": "[name]",
     "value": "[value]",
     "type": "[SensorType]"
}
```

avec `[name]` le nom du capteur (une chaîne de caractères), `[value]` la représentation en string de la valeur du capteur et `[sensorType]` le type de données parmi :

- 'POSITIVE_NUMBER',
- 'PERCENT',
- 'ON_OFF',
- 'OPEN_CLOSE'.
  
ou plus spécifiquement :

- 'TEMPERATURE',
- 'HUMIDITY',
- 'PRESSURE',
- 'SPEED',
- etc.

Un broker MQTT avec de faux capteurs et de fausses données est disponible à l'adresse suivante :

- `wss://random.pigne.org`

sur le port `443` pour le support WebSocket.

## Stockage des états       

Le plus simple est probablement d'utiliser l'idée du [*Lifting State Up*](https://reactjs.org/tutorial/tutorial.html#lifting-state-up) dans la démo de React.

## *Flexbox* ou *CSS Grid Layout*

Pour la mise en page de l'application, on utilisera au choix le mode de mise en page [`flexbox`](https://developer.mozilla.org/fr/docs/Web/CSS/Disposition_des_bo%C3%AEtes_flexibles_CSS/Utilisation_des_flexbox_en_CSS) ou ['CSS grid layouts'](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout), ou les deux.

## CSS Modules

On utilise les [CSS modules](https://github.com/css-modules/css-modules) pour gérer les feuilles de style.

On veillera à bien nommer les fichiers comme décrit dans la [documentation de `Vite`](https://vitejs.dev/guide/features#css-modules).

## Tests

Les composants React **doivent** être testés comme n'importe quel code JavaScript/TypeScript. Se référer à l'[aide en ligne de React sur les tests](https://fr.reactjs.org/docs/testing.html) pour tester le code.

## Travail à réaliser

- Travail à réaliser en **binôme**.
- Partir d'un nouveau projet créé avec `vite` (`npm create vite@latest mon-projet -- --template react-ts`) avec le template `react`, en `TypeScript`.
- Versionner le projet sur la forge.
- S'assurer que le projet est privé et m'ajouter en tant que développeur à votre projet.
- M'envoyer un mail avec le titre `"[M1-WEB] TP n°4"` et contenant les **nom** et **prénom** des membres du binôme ainsi que l'**URL du projet**.
- Faire des commits réguliers avec des messages clairs et réaliser le travail décrit ci-dessus.

**Ce TP DOIT être fait en binôme.**

## Échéance

- 4 mai 2025

## Évaluation

[Liste des aptitudes évaluées.](/teaching/WebDev1#react--reactrouter--redux)


