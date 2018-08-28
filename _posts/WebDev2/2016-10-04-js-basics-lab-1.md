---
layout: post
title: JavaSript Basics Lab 1
categories:
- WebDev2
- lab
author: Yoann Pigné
---

# JSBasicsLab [![pipeline status](https://www-apps.univ-lehavre.fr/forge/WEB-IHM/JSBasicsLab/badges/master/pipeline.svg)](https://www-apps.univ-lehavre.fr/forge/WEB-IHM/JSBasicsLab/commits/master)  [![coverage report](https://www-apps.univ-lehavre.fr/forge/WEB-IHM/JSBasicsLab/badges/master/coverage.svg)](https://www-apps.univ-lehavre.fr/forge/WEB-IHM/JSBasicsLab/commits/master)

Le code source nécessaire à ce tp se trouve sur GitHub : <https://www-apps.univ-lehavre.fr/forge/WEB-IHM/JSBasicsLab>

Ce projet est principalement constitué d'un fichier de tests (`test/index.test.js`) qui s'applique sur un ensemble de fonctions (`src/index.js`). Les tests sont écrits, mais le corps des fonctions testées est vide !

## technologies utilisées

- Ce projet est écrit en [ES6](http://www.ecma-international.org/ecma-262/6.0/index.html).
- le code est compilé en ES5 par [babel](https://babeljs.io/).
- Le respect des règles syntaxiques est effectué par [ESLint](https://eslint.org/).
- L'outils permettant l'exécution des tests est la plateforme [Jest](http://facebook.github.io/jest/).
- [NPM](https://www.npmjs.com/) est utilisé pour la gestion des dépendances et l'exécution des différents scripts.

## Travail à réaliser

1. Se connecter à la forge de l'université avec son propre login (se connecter avec le CAS de l'université la première fois).
1. "Forker" le projet (bouton "Fork").
1. Cloner son propre projet dans une copie de travail locale en utilisant le schema d'URL `https` (`git clone https://www-apps.univ-lehavre.fr/forge/USERNAME/JSBasicsLab.git`)
1. Dans la copie de travail  exécuter la commande `npm install` pour installer les dépendances du projet.
1. Lancer les tests : `npm test`
1. Il y a beaucoup d'erreurs. A vous de les corriger en écrivant le corps des fonctions dans le fichier `src/index.js` et en relançant les tests. Attention le style du code écrit est également testé. Les tests ne s'exécuteront pas si le code ne respectent pas les règles syntaxiques définies. On utilise les règles recommandées ([`eslint:recommended`](https://eslint.org/docs/rules/)) par le projet ESLint.
1. Faire des commits au fur et a mesure que vous validez des tests. (`git add -u && git commit -m "un test de plus..."`)

## Fin du travail

Quand tous les tests passent et que les modifications sont enregistrées (avec des `commit`) :

1. publiez votre projet : `git push`. Cela aura pour conséquence, en plus de publier vos modifications sur votre version du projet, de lancer l'exécution des tests unitaires sur le serveur d'intégration continue de la forge. 
1. créez un [merge request](https://docs.gitlab.com/ee/gitlab-basics/add-merge-request.html) pour que je puisse évaluer votre travail.
