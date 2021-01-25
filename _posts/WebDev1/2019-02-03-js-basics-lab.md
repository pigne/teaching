---
layout: post
title: JavaScript Basics Lab
categories:
- WebDev1
- lab
author: Yoann Pigné
published: true
update: 2021-01-12
---

# JS Basics Lab

Le code source nécessaire à ce tp se trouve sur la forge de L'université : <https://www-apps.univ-lehavre.fr/forge/2020-2021-M1/WEB-jsbasics-lab>

Ce projet est principalement constitué d'un fichier de tests (`test/index.test.js`) qui s'applique sur un ensemble de fonctions (`src/index.js`). Certains tests sont écrits, mais le corps des fonctions testées est vide ! D'autres fonctions sont écrites mais il faut écrire leurs testes. Parfois il manque le corps des fonctions et les tests.

## Travail à réaliser

- [ ] Se connecter à la [forge de l'université](https://www-apps.univ-lehavre.fr/forge) avec son propre login (se connecter avec le CAS de l'université la première fois). :warning: Bien s'assurer que **GIT est correctement configuré**. Pour plus d'information sur GIT, GitLab et la forge de l'université, se référer au [cours introductif au travail en équipe à l'université](https://pigne.org/teaching/general/lecture/Gestion-de-version-travail-en-equipe).
- [ ] "*Forker*" ou "diverger" le projet (bouton "*Fork*" ou "Divergence").
- [ ] Cloner son propre projet dans une copie de travail locale en utilisant le schema d'URL `https` (`git clone https://www-apps.univ-lehavre.fr/forge/LOGIN/WEB-jsbasics-lab.git`) (sans oublier de changer `LOGIN` par votre login).
- [ ] Modifier le fichier README.md du projet pour y faire apparaître vos coordonnées (nom, prénom, login, email).
- [ ] Dans la copie de travail exécuter la commande `npm install` ou `npm i` pour installer les dépendances du projet.
- [ ] Lancer les tests : `npm test` ou `npm t`
- [ ] Il y a beaucoup d'erreurs. A vous de les corriger en écrivant le corps des fonctions dans le fichier `src/index.js` (à la place des commentaires `/* TODO */`) et en relançant les tests. Faire des commits **au fur et à mesure** que vous validez des tests. (`git add -u && git commit`)
- [ ] A l'inverse certaines fonctions sont écrites dans `src/index.js` mais ne sont pas testées. A vous d'écrire les tests dans `src/index.test.js` afin de **maximiser le taux de couverture des tests**. Le taux de couverture s'affiche en ligne de commande dans le tableau à la fin des tests. Pour avoir des infos détaillées on peut ouvrir le fichier `coverage/lcov-report/index.html` dans un navigateur pour comprendre quelles lignes et quelles branches ont été oubliées. Faire des commits **au fur et à mesure** que vous écrivez les tests.

:warning: Le style du code écrit est également testé. Les tests ne s'exécuteront pas si le code ne respectent pas les règles syntaxiques définies. On utilise les règles recommandées ([`eslint:recommended`](https://eslint.org/docs/rules/)) par le projet ESLint.

## Fin du travail

Quand tous les tests passent, que la couverture du code par les tests est satisfaisante (100%, 99%, 98%, ...?) et que les modifications sont enregistrées (avec des `commit`) :

1. publiez votre projet : `git push`. Cela aura pour conséquence, en plus de publier vos modifications sur votre version du projet, de lancer l'exécution des tests unitaires sur le serveur d'intégration continue de la forge.
2. créez un [merge request](https://docs.gitlab.com/ee/gitlab-basics/add-merge-request.html) pour que je puisse évaluer votre travail.

## technologies utilisées

- Ce projet est écrit en [ES6](http://www.ecma-international.org/ecma-262/6.0/index.html).
- le code est compilé en ES5 par [babel](https://babeljs.io/).
- Le respect des règles syntaxiques est effectué par [ESLint](https://eslint.org/).
- L'outils permettant l'exécution des tests est la plateforme [Jest](http://facebook.github.io/jest/).
- [NPM](https://www.npmjs.com/) est utilisé pour la gestion des dépendances et l'exécution des différents scripts.

## Échéance

TP à rendre pour le :

- Groupe A : 21 janvier 2021
- Groupe B : 19 janvier 2021
- Groupe C : 26 janvier 2021

## Évaluation

[Liste des aptitudes évaluées.](/teaching/WebDev1#js-basics)