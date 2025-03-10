---
layout: post
title: Whiteboard WebSocket Lab
categories:
- WebDev1
- lab
author: Yoann Pigné
published: true
---

Un **tableau blanc** sur une Web App est une surface sur laquelle les utilisateurs peuvent dessiner. Chaque utilisateur possède une **couleur unique** et voit en **temps réel** les autres participants dessiner avec leur propre couleur.  

Un utilisateur doit pouvoir :  
- **Créer** un nouveau dessin vierge.  
- **Consulter** la liste des dessins en cours.  
- **Rejoindre** un dessin existant et y contribuer.  

Le dessin sera réalisé à l'aide de l'élément HTML5 [`<canvas>`](https://developer.mozilla.org/fr/docs/Web/Guide/Graphics/Dessiner_avec_canvas).  

Vous partirez du projet de base proposé :  
[https://www-apps.univ-lehavre.fr/forge/2024-2025-m1/WEB-whiteboard-websocket-lab](https://www-apps.univ-lehavre.fr/forge/2024-2025-m1/WEB-whiteboard-websocket-lab), qui implémente déjà un chat en WebSocket. Vous ajouterez une interface de dessin permettant une collaboration en temps réel.  

**Contraintes techniques :**  
- Utilisation exclusive des **WebSockets** et du **Canvas** supportés nativement par le navigateur.  
- Aucun framework ni bibliothèque externe de dessin ne doit être utilisé.  

### **Problématique à résoudre**  
Lorsqu'un utilisateur rejoint un dessin en cours, il reçoit les nouvelles modifications en temps réel. Cependant, il ne voit pas par défaut les dessins réalisés avant sa connexion.  
- Proposez une solution pour lui permettre de récupérer l'historique du dessin.  

### **Bonus**
- **Gestion des utilisateurs** : Afficher la liste des utilisateurs actuellement connectés et leur couleur respective.
- **Sauvegarde des dessins** : Permettre de sauvegarder un dessin pour le retrouver ultérieurement.
- **Historique des dessins** : Permettre de revenir en arrière dans l'historique des dessins.
  
## Travail à réaliser

- Travail à réaliser en **binome**
- Faire une divergence du projet de base [https://www-apps.univ-lehavre.fr/forge/2024-2025-m1/WEB-whiteboard-websocket-lab](https://www-apps.univ-lehavre.fr/forge/2024-2025-m1/WEB-whiteboard-websocket-lab)
- S'assurer que votre projet est bien privé.
- M'ajouter en tant que développeur à votre projet.
- M'envoyer un mail avec le titre `" [M1-WEB] TP n°3 "` avec les  **nom** et  **prénom** des membres du binôme ainsi que l'**URL du projet**. 
- Faire des commits régulier avec des messages claires et réaliser le travail décrit au dessus. 


## Échéance

TP à rendre pour le :25/03/2025

## Évaluation

[Liste des aptitudes évaluées.](/teaching/WebDev1#websocket)

