---
layout: syllabus
matiere: WebDev2
title: Développement Web (M2)
subtitle: M2 iWOCS - WEB - Université Le Havre Normandie
order: 3
published: true
---
## Outils, protocoles, standards et langages du Web
**Master 2 Informatique IWOCS — Université Le Havre Normandie**  
**Volume horaire :** 45h (15h CM, 30h TD/TP)  

---

###  Objectifs pédagogiques

Cette UE vise à consolider et approfondir les compétences en **développement Web full-stack** à l’aide des technologies modernes de l’écosystème TypeScript.  
Les étudiants apprennent à concevoir une **application complète** intégrant :

1. un **serveur Web full-stack** avec génération côté serveur (SSR),  
2. un **Web Service** interopérable (REST ou GraphQL),  
3. une **interface de visualisation dynamique** des données.

L’ensemble est mis en pratique autour d’un **fil rouge d’application** :  
> la gestion et la visualisation d’annonces immobilières.

---

###  Contenu du cours

#### Partie 1 — Serveur full-stack et SSR
- Framework Web **Next.js** (App Router)  
- Modélisation et base de données avec **Prisma ORM**  
- Authentification et rôles avec **NextAuth**  
- Gestion des formulaires et rendu SSR  
- Tests unitaires et d’intégration

#### Partie 2 — Web Services et API
- Architectures REST et GraphQL  
- Conception et documentation d’API (OpenAPI / Swagger)  
- Sécurisation et gestion des accès (JWT, rôles, middleware)  
- Communication client / service (fetch, axios, Apollo)

#### Partie 3 — Visualisation et intégration Front
- Composants front modernes (React, Next.js côté client)  
- Représentation de données : **Recharts**, **D3.js**, etc.  
- Interaction et filtres dynamiques  
- Construction d’un tableau de bord des données

---

###  Travaux pratiques et évaluation

Chaque partie donne lieu à un **TP noté**, menant progressivement à une application complète.  
L’évaluation repose sur :

- la **qualité technique** (architecture, typage, tests),  
- la **pertinence fonctionnelle** (authentification, rôles, CRUD, API),  
- la **clarté de l’interface et de la visualisation**,  
- et une **soutenance orale** de présentation du projet.

> Évaluation continue + oral final.  
> Travail en **binôme** sur la forge universitaire.

---

###  Compétences visées

| Domaine | Code | Intitulé |  
|----------|------|----------|  
| D1 | C4 | Sécurité, tests, robustesse |  
| D2 | C1 | Développement Web (frontend / backend) |  
| D2 | C2 | Conception d’API et protocoles de communication |  
| D2 | C3 | Architecture logicielle et intégration |  
| D4 | C1 | Travail collaboratif, Git, forge universitaire |

---

### 🧰 Prérequis
- Bases du développement Web vues en **M1 IWOCS**  
- Notions de **JavaScript / TypeScript**, **HTTP**, **bases de données relationnelles**

---

### 🔑 Mots-clés
Next.js, TypeScript, Prisma, NextAuth, REST, GraphQL, SSR, Recharts, D3.js, ORM, API, full-stack, data visualization.

### Evaluations et aptitudes
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th></th>
      <th>Serveur Fullstack</th>
      <th>Web Services</th>
      <th>Visualisation / Front</th>
    </tr>
    <tr>
      <th>Domaine</th>
      <th>Compétence</th>
      <th>Aptitude</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>D1</th>
      <th>D1.C4</th>
      <th>Maîtriser l’écriture des tests, la sécurité et la robustesse du code</th>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
    </tr>
    <tr>
      <th rowspan="5" valign="top">D2</th>
      <th rowspan="3" valign="top">D2.C1</th>
      <th>Backend : Framework Web Next.js (SSR, ORM, Auth)</th>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>Frontend : Formulaires, pages dynamiques et responsive design</th>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>Frontend : Visualisation de données (Recharts, D3, etc.)</th>
      <td></td>
      <td></td>
      <td>✓</td>
    </tr>
    <tr>
      <th>D2.C2</th>
      <th>Concevoir et exposer des Web Services (REST, GraphQL)</th>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
    </tr>
    <tr>
      <th>D2.C3</th>
      <th>Concevoir et structurer une architecture logicielle modulaire</th>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
    </tr>
    <tr>
      <th>D4</th>
      <th>D4.C1</th>
      <th>Collaborer avec Git / travail en groupe</th>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
    </tr>
  </tbody>
  </table>
