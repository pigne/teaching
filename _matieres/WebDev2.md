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

1. un **serveur Web full-stack** à composants serveur,  
2. un **Web Service** interopérable (REST **et** GraphQL),  
3. une **interface de visualisation dynamique** des données.

Le fil conducteur de l’année est le **parcours d’une donnée**, de son stockage
jusqu’à sa représentation graphique — et ce que l’on risque de perdre à chaque
frontière traversée (typage, sécurité, performance) :

```
schéma de données → types → validation → frontière réseau
                  → contrat d’API → types du client → composant → pixels
```

Le domaine d’application est le **foncier et l’immobilier**.

---

###  Contenu du cours

#### Partie 1 — Serveur full-stack et composants serveur
- Framework Web **Next.js** (App Router)  
- **Composants serveur / composants client** : où s’exécute le code, et pourquoi c’est décisif  
- **Server Actions** : muter des données sans écrire d’API  
- Modélisation et persistance avec **Prisma ORM** sur **PostgreSQL** (via Docker)  
- **Validation des entrées au runtime** avec **Zod**  
- **Authentification** (Better Auth) et **autorisation** : rôles, propriété des ressources  
- Tests unitaires et d’intégration ; mesure du comportement (requêtes SQL, rendu serveur)

#### Partie 2 — Web Services et API
- Architectures REST et GraphQL  
- Conception et documentation d’API (OpenAPI / Swagger), approches *code-first* et *contract-first*  
- Sécurisation et gestion des accès (JWT, rôles, middleware)  
- Sécurité applicative : OWASP API, CORS, limitation de débit  
- Cache HTTP et versionnement d’API  
- Communication client / service, génération de clients typés

#### Partie 3 — Visualisation et intégration Front
- Le **pipeline de visualisation** : données → transformation → mise en page → encodage → rendu → interaction  
- Les modèles de rendu du navigateur : **SVG**, **Canvas**, **WebGL** — et le choix selon le volume  
- Bibliothèques : **D3.js**, **Vega**, **Observable Plot**… ; intégration avec React  
- Interaction, filtres dynamiques, accessibilité des représentations  
- **Maîtrise du volume de données échangé** entre le service et le client

---

###  Travaux pratiques et évaluation

Chaque partie donne lieu à un **TP noté**, suivi d’une **évaluation orale**.

| TP | Sujet | Format |
|---|---|---|
| 1 | Serveur full-stack | binôme |
| 2 | Web Services | binôme |
| 3 | Visualisation | groupe de 4 (modèle + import + API + front) |

Le travail est hébergé sur la **forge universitaire**.

#### Sur quoi porte la note

Le code d’une application de ce type est aujourd’hui largement générable
automatiquement. **L’usage d’assistants IA est donc autorisé**, et l’évaluation
porte sur ce qui n’est pas délégable :

- des **cibles mesurées** et vérifiables (nombre de requêtes à la base, volume
  de données transféré, codes de statut HTTP, tests qui passent) ;
- la **compréhension du code rendu**, évaluée à l’oral, machine ouverte, en
  naviguant dans le projet ;
- le **jugement critique**, tracé dans un fichier `AI.md` qui documente ce qui
  a été délégué, ce qui a été refusé, et pourquoi.

S’y ajoutent la qualité de l’architecture et du typage, la pertinence
fonctionnelle (authentification, autorisation, API) et la clarté des
représentations graphiques.

---

###  Compétences visées

| Domaine | Code | Intitulé |  
|----------|------|----------|  
| D1 | C4 | Sécurité, tests, robustesse, mesure du comportement |  
| D2 | C1 | Développement Web (frontend / backend) |  
| D2 | C2 | Conception d’API et protocoles de communication |  
| D2 | C3 | Architecture logicielle et intégration |  
| D4 | C1 | Travail collaboratif, Git, forge universitaire |

---

### 🧰 Prérequis
- Bases du développement Web vues en **M1 IWOCS** (TypeScript, React)  
- Notions de **HTTP**, de **SQL** et de **bases de données relationnelles**  
- **Docker** et **Git** (couverts ailleurs dans le master, utilisés ici comme acquis)

---

### 🔑 Mots-clés
Next.js, App Router, composants serveur, Server Actions, TypeScript, Prisma,
PostgreSQL, Zod, Better Auth, autorisation, REST, GraphQL, OpenAPI, JWT, cache
HTTP, D3.js, Vega, SVG / Canvas / WebGL, ORM, API, full-stack, visualisation de
données.

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
      <th>Maîtriser l’écriture des tests, la sécurité, la robustesse du code, et savoir mesurer le comportement d’une application</th>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
    </tr>
    <tr>
      <th rowspan="5" valign="top">D2</th>
      <th rowspan="3" valign="top">D2.C1</th>
      <th>Backend : Next.js (composants serveur, Server Actions), ORM, authentification et autorisation</th>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>Frontend : formulaires validés, pages dynamiques, accessibilité, responsive design</th>
      <td>✓</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>Frontend : visualisation de données (D3, Vega, SVG / Canvas / WebGL)</th>
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
