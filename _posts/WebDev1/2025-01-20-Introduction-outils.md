---
layout: post
title: Introduction aux Outils de Développement
categories:
- WebDev1
- lecture
author: Yoann Pigné
published: true
---


## Environnements et Éditeurs de Code

Un bon éditeur de code est indispensable pour tout développeur. Aujourd’hui, Visual Studio Code (VS Code) est l'un des choix les plus populaires. Il offre un écosystème riche d'extensions qui améliorent la productivité et la qualité du code.

### Visual Studio Code

- **Extensions essentielles** :
  - ESLint : Analyse statique du code pour respecter les règles de style et d’erreur.
  - Prettier : Formatage automatique pour un code lisible et cohérent.
  - Live Server : Prévisualisation de pages HTML avec rechargement automatique.
- Fonctionnalités clés : coloration syntaxique, snippets, terminal intégré.

### Autres éditeurs populaires

- **WebStorm** : Un IDE puissant pour JavaScript et TypeScript.
- **Sublime Text** : Rapide et personnalisable.
- **Atom** : Flexible et open-source (non activement développé).

##  Navigateurs et Outils de Développement

Les outils de développement des navigateurs sont essentiels pour déboguer et optimiser les applications web.

###  Navigateurs pour développeurs

- **Chrome DevTools** : Inclus avec Google Chrome.
- **Firefox Developer Edition** : Une version spéciale de Firefox avec des outils de développement avancés.

###  Outils intégrés

- **DOM/CSS Inspector** : Inspection des éléments HTML et des styles.
- **Javascript Console** : Exécution de commandes et affichage des erreurs.
- **Network Tab** : Analyse des requêtes HTTP.
- **Performance Monitor** : Suivi des temps de chargement et du rendu.

## Gestion des Dépendances et du Versioning

###  Node.js et Gestion des paquets
Node.js est un environnement d’exécution pour JavaScript côté serveur, associé à des gestionnaires de paquets comme npm ou yarn.

####  Installation et Utilisation

- Installation recommandée via [gestionnaire de paquets](https://nodejs.org/en/download/package-manager/).
- Utilisation basique :

```bash
mkdir monProjet && cd monProjet
npm init -y # Initialise un projet avec un package.json par défaut
npm install lodash --save # Installe lodash et l’ajoute aux dépendances
```

####  pnpm : Gestionnaire de paquets performant

- Alternative moderne à npm et yarn, économisant de l’espace disque grâce au partage de fichiers entre projets.

###  Versioning avec Git et GitHub

Git permet de suivre les modifications du code source et de collaborer efficacement.

####  Concepts de base

- **Repository** : Dépôt de code.
- **Branch** : Version isolée d’un projet.
- **Pull Request** : Proposition de modification.

####  Commandes utiles

```bash
git init # Initialise un dépôt

git add . # Ajoute les modifications en staging
git commit -m "Initial commit" # Enregistre un snapshot
git push origin main # Envoie les modifications vers le dépôt distant
```

GitHub est une plateforme pour héberger et collaborer sur des projets. Elle supporte les workflows comme les **Issues** et **Actions** pour l’intégration continue.

### Support des TP pour le cours de WEB-IHM

Les TP de ce cours sont hébergé sur un gestionnaire de projet GIT. Les TP sont a faire en clonant le dépôt d'origine. Le dépôt  se fait en faisant un *pull/merge request* sur le dépôt d'origine.

On utilise la forge de l'université du Havre (c'est une instance Gitlab): <https://www-apps.univ-lehavre.fr/forge>.
