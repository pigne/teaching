---
layout: post
title: "Web App Lab #1"
categories:
- JavaWebAppTools
- lab
author: Yoann Pigné
---


## Travail demandé

On désire réaliser une application qui permet à un utilisateur, sans qu'il se connecte, de gérer différents compteurs de temps. Il doit pouvoir ajouter, modifier ou supprimer des compteurs.

Pour chaque compteur il doit pouvoir :
- définir un titre pour ce compteur
- définir la langue à partir d'une liste proposée, qui permet de définir une locale
- définir une échéance dans le temps pour ce compteur (date et heure, dans la locale définie par la langue)

Un compteur, une fois défini, avec sa langue et son échéance,  s'affiche sur la page et se met à jour, chaque seconde. La mise à jour du compteur est effectuée **sur le serveur** et **pas** en javascript sur le client. Seule la nouvelle valeur est envoyée en JSON toutes les secondes. Pour éviter que le client ne fasse trop de requêtes de mise à jours sur le serveur, on a recours à un nouveau mode de communication : les **WebSockets** qui permettent au serveur d'envoyer régulièrement des informations au client sans que celui ci n'ai besoin d'en faire la demande à chaque fois, à la manière d'une *socket* réseau classique.

Les utilisateurs sont identifiés par des cookies de sessions (pas d'authentification, pas de login). Un utilisateur ayant définit des compteurs dans le passé doit pouvoir les récupérer plus tard dans la mesure où il se connecte sur la même machine avec le même navigateur, et où les cookies sont activés et n'ont pas été effacés.

Dans la mesure our les fonctionnalités demandées ici sont développées, toute fonctionnalité supplémentaire est la bienvenue.

## Développement

L'application doit être développée selon les spécifications de **Java EE**.

Tous les calculs de temps sont faits **coté serveur**.

Il est possible d'utiliser une base de données pour stoker les compteurs. Un stockage en mémoire du serveur est néanmoins toléré. Pourquoi ne pas utiliser une base de données de type ["key-value"](https://en.wikipedia.org/wiki/Key-value_database) ?

Le code est "versionné" avec GIT et hébergé sur un dépôt GIT et le projet est construit grâce a Maven.

Note : Tomcat possède une implémentation de la norme [JSR 356, *API for WebSocket*](http://www.oracle.com/technetwork/articles/java/jsr356-1937161.html).


## Tests

Des tests Unitaires vérifient le fonctionnement normal de chaque composant. Les tests doivent se lancer lors de l'exécution de la commande `mvn test`.

## Travail en Equipe

Il est possible de s'organiser en petite équipe. Une équipe sera composée au maximum de 4 personnes.

Pour les équipes de plus d'une personne, la bonne répartition des tâches et l'équilibre des contributions de chacun seront pris en compte.

## GIT

Le projet est hébergé sur un dépôt GIT.

Tout le monde doit avoir un compte proprement configuré pour que les commits soient signés.

Chaque équipe m'envoie une liste définitive de membres (nom, prénom, pseudo) et me donne accès en lecture à son dépôt.


Optionnel : Vous disposez d'une instance GitLab (un clone de GitHub) privée, à l'université, si vous le souhaitez : <https://www-apps.univ-lehavre.fr/forge>

Mes identifiants :

- github.com: "pigne"
- bitbucket.org: "yoann.pigne@gmail.com"
- gitlab.com: "yoann.pigne"
- www-apps.univ-lehavre.fr/forge : "pigne"

## Evaluation

La note tiendra compte :
- du nombre de fonctionnalités développées,
- de la qualité du code et des tests,
- de la possibilité ou non de lancer le projet sur une Ubuntu 16.04 avec un simple `mvn install`,
- de la bonne répartition des contributions dans l'équipe (∀ équipe, |équipe| > 1),

Plus grandes les équipes, plus importants seront les résultats attendus...  

## Deadline


 ***Dimanche 20 Novembre 2016, à 17h,*** j'effectuerais un `git pull` de vos projets auquel vous m'aurez donné accès.
