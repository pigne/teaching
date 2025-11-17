---
layout: post
title: Web Services
categories:
- WebDev2
- lecture
author: Yoann Pigné
published:  true
---


## Concepts acquis 

- **Comprendre une architecture web full-stack** : Front (SSR) + backend + DB + Auth dans un même projet
- **Utiliser un ORM moderne (Prisma)**  : Création et requêtes sur des modèles typés
- **Manipuler des routes API dans Next.js**  :    `GET`, `POST`, `PATCH`/`PUT`, `DELETE` basiques
- **Gérer des utilisateurs et rôles** : Session, authentification, accès conditionnels     
- **Comprendre la séparation logique** : Front SSR ↔ API interne ↔ base de données           

## Nouveau concepts

- **Web Service** : Les routes API de Next.js sont déjà des WS REST
- **REST**:  URI + verbes HTTP (GET, POST, DELETE)
- **Ressource** : Les « annonces », « utilisateurs », « questions » du TP1 sont des ressources
- **API externe** :   Il suffit d’extraire la logique hors Next.js SSR
- **OpenAPI (Swagger)** : Formaliser la description des endpoints
- **GraphQL** : Variante du même principe, plus flexible
- **Sécurité (JWT, scopes)** : Extension naturelle de l’authentification du TP1 


## REST

### Principe et modèles d’URI

REST manipule des ressources.
Une ressource = un objet de votre domaine.

Exemples pour le fil rouge :

```
GET /annonces
GET /annonces/42
POST /annonces
PATCH /annonces/42
DELETE /annonces/42
```

---

### Requêtes stateless et performances

REST est **sans état** (*stateless*) :

* chaque requête contient toutes les informations nécessaires,
* le serveur n’a pas besoin de conserver de contexte.

Conséquences :

* beaucoup plus facile à mettre en cache,
* scalabilité simplifiée,
* comportement prévisible.

---

### Formats, erreurs, pagination

REST ne force aucun format, mais JSON domine.

Pagination classique :

```
GET /annonces?page=1&limit=20
```

Erreurs :

```json
{
  "error": "Not Found",
  "status": 404
}
```


## GraphQL

GraphQL expose **un unique endpoint** (souvent `/graphql`) auquel le client envoie une requête structurée :

```graphql
query {
  annonces {
    id
    titre
    prix
  }
}
```

✔ Le client choisit exactement les champs
✔ Une seule requête peut récupérer des données liées
✔ Idéal pour une interface riche en données


### Langage de requêtes et schéma

GraphQL repose sur deux éléments clés :

* un **schéma** (`schema`), qui décrit les types de données et les relations disponibles,
* des **queries** et **mutations**, écrites dans un langage dédié, pour interroger ou modifier les données.

Exemple simplifié :

```graphql
type Annonce {
  id: ID!
  titre: String!
  prix: Int!
  surface: Int
  ville: String
}

type Query {
  annonces: [Annonce!]!
  annonce(id: ID!): Annonce
}

type Mutation {
  creerAnnonce(titre: String!, prix: Int!): Annonce!
}
```


---

### Résolveurs et optimisation des données

Les **resolvers** sont des fonctions qui indiquent **comment récupérer ou calculer les champs du schéma**.

Exemple :

```ts
const resolvers = {
  Query: {
    annonces: () => prisma.annonce.findMany(),
    annonce: (_: any, args: { id: number }) => prisma.annonce.findUnique({ where: { id: args.id } })
  },
  Mutation: {
    creerAnnonce: (_: any, args: { titre: string; prix: number }) =>
      prisma.annonce.create({ data: { titre: args.titre, prix: args.prix, agentId: 1 } })
  }
};
```

* Chaque champ peut avoir son propre resolver.
* Les résolveurs sont **appelés uniquement si le client les demande**, ce qui permet de **réduire le volume de données transférées** par rapport à REST classique.

Attention:

* aux requêtes trop profondes,
* aux surcoûts serveur en cas d’absence de garde-fous.

---


### Avantages de GraphQL

* **Flexibilité pour le client** : possibilité de demander exactement les champs souhaités.
* **Regroupement des requêtes** : on peut combiner plusieurs données en une seule requête.
* **Typage strict** : le schéma impose la structure des données, ce qui facilite la validation et la documentation.
* **Interopérabilité** : compatible avec n’importe quel front-end (React, Vue, mobile…).

---

### Optimisation et bonnes pratiques

* Limiter la **profondeur des requêtes** pour éviter les appels trop lourds.
* Utiliser des **dataloaders** ou caches côté serveur pour prévenir le *N+1 problem*.
* Documenter le schéma pour que les développeurs front-end sachent exactement quelles requêtes sont possibles.
* Mettre en place une **authentification et des permissions** pour protéger les champs sensibles.


**Pour aller plus loin avec GraphQL**  <https://graphql.org/learn>



## SOAP

Ancien standard industriel basé sur XML et WSDL.
Approche très différente : SOAP expose **des opérations** (et non des ressources), par exemple :

```
upgradeAccount(UserId)
getUserCredit(UserId)
```

✔ Très rigoureux
✔ Protocoles de sécurité avancés (WS-Security)
✔ Utilisé en banque, santé, SI critiques

Mais :

✖ Verbeux
✖ Difficile à manipuler
✖ Peu adapté aux architectures Web modernes




## gRPC

gRPC est la technologie la plus performante actuellement pour des micro-services internes.

* Communication binaire via **Protocol Buffers**
* HTTP/2
* Streaming bidirectionnel
* Un fichier `.proto` définissant les services
* Fort typage du contrat

Exemple :

```proto
service LocationService {
  rpc GetAnnonce (AnnonceRequest) returns (AnnonceResponse);
}
```


✔ Très performant
✔ Excellent pour des systèmes distribués
✔ Pas idéal pour le Web sans passerelle gRPC-Web

 

### Comparatif REST GraphQL / SOAP / gRPC

| Critère                    | REST                | GraphQL                          | SOAP                      | gRPC                    |
| -------------------------- | ------------------- | -------------------------------- | ------------------------- | ----------------------- |
| **Modèle**                 | Ressources          | Schéma + requêtes                | Services (procédures)     | RPC                     |
| **Format**                 | JSON (souple)       | JSON                             | XML strict                | Protobuf (binaire)      |
| **Endpoint(s)**            | Plusieurs           | Un seul                          | Plusieurs                 | Plusieurs               |
| **Typage**                 | Faible              | Fort (schéma)                    | Très fort (WSDL/XSD)      | Très fort (.proto)      |
| **Performance**            | Bonne               | Variable (requêtes complexes)    | Moyenne                   | Excellente              |
| **Cache HTTP**             | ✔ ✔                 | ✖                                | ✖                         | ✖                       |
| **Courbe d’apprentissage** | Facile              | Moyenne                          | Élevée                    | Élevée                  |
| **Cas d’usage**            | Web publics, mobile | Interfaces riches, multi-sources | Entreprises, SI critiques | Micro-services internes |




## Swagger / OpenAPI : Documentation, conception et tests d’API

Swagger (aujourd’hui basé sur la spécification **OpenAPI**) est un ensemble d’outils permettant de **décrire**, **documenter**, **concevoir** et **tester** une API.  
C’est l’outil standard pour toutes les API REST modernes, et un incontournable pour les équipes travaillant en CI/CD.

---

### Pourquoi Swagger ?

Une API REST peut devenir difficile à maintenir lorsque :

- le nombre d’endpoints augmente,
- plusieurs développeurs interviennent,
- il faut générer des SDK clients (TS, Python, Java…),
- l’on souhaite automatiser les tests ou la validation des requêtes.

Swagger répond précisément à ces problématiques avec :

✔ une **documentation interactive** (Swagger UI)  
✔ un **contrat d’API** en YAML/JSON  
✔ la **validation automatique** des schémas  
✔ la **génération de clients / serveurs**  
✔ l’intégration dans l’écosystème DevOps / CI

---

### Les trois outils Swagger

| Outil | Rôle |
|-------|------|
| **Swagger Editor** | Écriture et validation d’un schéma OpenAPI : <https://editor.swagger.io/> |
| **Swagger UI** | Documentation interactive  |
| **Swagger Codegen / OpenAPI Generator** | Génération du code client/serveur dans nimporte quel langage |

---

### Structure d'une spécification OpenAPI

Une spec OpenAPI est un fichier unique (`openapi.yaml` ou `openapi.json`) composé de :

```yaml
openapi: 3.1.0
info:
  title: ImmoService API
  version: 1.0.0

paths:
  /annonces:
    get:
      summary: Liste des annonces
      responses:
        '200':
          description: OK

components:
  schemas:
    Annonce:
      type: object
      properties:
        id:
          type: integer
        titre:
          type: string
````

Trois parties structurent 95% d’une API :

1. `paths` → endpoints REST
2. `components/schemas` → modèles de données
3. `components/parameters` et `requestBody` → schémas de validation

---

### Conception d’une API (approche Contract-First)

Swagger permet une approche **Contract First** :

1. On écrit d’abord la spécification OpenAPI
2. Le fichier devient le **contrat** entre front-end et back-end
3. On génère :

   * le serveur (squelettes d’endpoints)
   * le client (SDK auto-typé)
   * les mocks (tests automatisés)

Avantages :

* pas d’ambiguïté entre front et back,
* validation centralisée,
* documentation toujours à jour.

---

### Paramètres d'API : les 3 formes principales

Swagger permet de définir trois types de paramètres.

#### 1. Paramètres de requête (query)

```yaml
parameters:
  - name: minPrix
    in: query
    schema:
      type: integer
```

Correspond à :
`GET /annonces?minPrix=150000`

---

#### 2. Paramètres d’URL (path)

```yaml
/annonces/{id}:
  get:
    parameters:
      - name: id
        in: path
        required: true
        schema:
          type: integer
```

Correspond à :
`GET /annonces/42`

---

#### 3. Corps de requête (body)

```yaml
requestBody:
  required: true
  content:
    application/json:
      schema:
        $ref: '#/components/schemas/AnnonceInput'
```

---

### Exemple complet d’endpoint avec schéma

```yaml
/annonces:
  post:
    summary: Créer une annonce
    requestBody:
      required: true
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/CreateAnnonceDto'
    responses:
      '201':
        description: Annonce créée
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Annonce'
```

---

### Documentation interactive : Swagger UI

Swagger UI permet d’exécuter directement les requêtes depuis le navigateur :

* champ pour les paramètres,
* aperçu automatique du JSON,
* codes HTTP documentés,
* affichage des erreurs.

Dans NestJS (par exemple) :

```ts
async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  const config = new DocumentBuilder()
    .setTitle('ImmoService API')
    .setDescription('Documentation de l’API REST immobilière')
    .setVersion('1.0')
    .addBearerAuth()
    .build();

  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup('api', app, document);

  await app.listen(3000);
}
bootstrap();
```

L’interface est accessible via :

```
http://localhost:3000/api
```

---

### Tests API avec Swagger

Swagger peut générer automatiquement :

* un client JavaScript TypeScript pour appeler l’API,
* des mocks pour tests,
* une validation des contrats d’entrée/sortie.

Exemple :

```bash
openapi-generator-cli generate \
  -i openapi.yaml \
  -g typescript-fetch \
  -o ./sdk
```

Le front peut ensuite importer :

```ts
import { AnnoncesApi } from './sdk';

const api = new AnnoncesApi();
api.getAnnonces().then(console.log);
```

➡️ Aucun code manuel, tout est typé et validé.

---

### Bonnes pratiques Swagger

✔ Regrouper les modèles dans `components/schemas`
✔ Documenter tous les codes d’erreur (400/401/403/404/500)
✔ Versionner la spec (`v1`, `v2`…)
✔ Générer les clients automatiquement (CI)
✔ Utiliser `examples:` pour illustrer l’API
✔ Ajouter les règles de sécurité (Bearer, API Key, OAuth2)

---

### Limites de Swagger / OpenAPI

* Ne gère pas graphiquement les relations profondes (contrairement à GraphQL).
* Ne permet pas au client de choisir précisément les champs retournés.
* Peut devenir verbeux sur une grosse API (>150 endpoints).
* Le versioning peut devenir complexe si les modèles sont partagés entre endpoints.

---

### Swagger vs GraphQL Playground

| Critère                   | Swagger (OpenAPI)        | GraphQL Playground |
| ------------------------- | ------------------------ | ------------------ |
| Type d’API                | REST                     | GraphQL            |
| Documentation             | Détaillée, contractuelle | Déduite du schéma  |
| Client choisit les champs | ❌ non                    | ✅ oui              |
| Validation                | Forte, statique          | Forte, typée       |
| Explorateur interactif    | Oui                      | Oui                |
| Génération de SDK         | Excellente               | Excellente         |
| Versioning                | Explicite (v1, v2…)      | Plus délicat       |





##  Approches pour documenter une API : Code-First vs Contract-First

La documentation d’une API peut être produite de deux manières différentes :

1. **Approche Code-First**
   → La documentation est générée automatiquement à partir du code du backend.
2. **Approche Contract-First**
   → Le développeur écrit manuellement un fichier OpenAPI (`swagger.yaml`) qui sert de contrat.

Les deux approches coexistent aujourd’hui, chacune avec des avantages et des cas d’usage différents.

---

### 1.  Approche **Code-First** (décorateurs)

Dans l’approche Code-First, c’est le **code source de l’application** qui sert de source de vérité pour générer la documentation.
Les développeurs annotent leur code avec des **décorateurs** qui décrivent l’API :

* routes,
* paramètres,
* types de données,
* réponses,
* erreurs.

Le framework se charge ensuite de produire :

* un **document OpenAPI** (généralement `openapi.json`),
* une **interface Swagger UI** prête à l’emploi.

---

#### Exemple de framework Code-First : **NestJS**

NestJS est aujourd’hui l’un des meilleurs exemples car il intègre Swagger de manière native.

```ts
@ApiTags('annonces')
@Controller('annonces')
export class AnnoncesController {
  
  @Get()
  @ApiOperation({ summary: 'Liste les annonces' })
  @ApiResponse({ status: 200, type: [AnnonceDto] })
  findAll() {
    return this.service.findAll();
  }
}
```

→ Avec ces décorateurs, NestJS génère automatiquement :

* `/api-json` → le document OpenAPI
* `/api` → l’interface Swagger UI

Aucun fichier YAML n’est nécessaire.

---

#### Autres frameworks *Code-First*

| Framework                   | Langage    | Méthode                         | Notes                                       |
| --------------------------- | ---------- | ------------------------------- | ------------------------------------------- |
| **FastAPI**                 | Python     | Type hints + décorateurs        | Génére automatiquement OpenAPI + Swagger UI |
| **ASP.NET Core**            | C#         | Attributs + analyse automatique | Très mature, support officiel OpenAPI       |
| **LoopBack 4**              | TypeScript | Décorateurs + modèles           | Full code-first Node.js                     |
| **Spring Boot (SpringDoc)** | Java       | Annotations                     | OpenAPI généré à partir des annotations     |


Dans tous ces cas, **le code prime** :
les décorateurs et les types définissent la documentation.

---

#### ✔️ Avantages du Code-First

* Documentation **toujours à jour** (liée au code).
* Pas besoin d’écrire de YAML.
* Améliore la productivité.
* Réduit les erreurs entre “spec” et “implémentation”.
* Génération d’outils automatique (SDK client, tests, etc.).

#### ❌ Limites

* Plus difficile si l’équipe veut concevoir l’API *avant* d’écrire le code.
* Certains projets legacy ou polyglottes préfèrent un contrat indépendant du backend.

---

### 2.  Approche **Contract-First** (`swagger.yaml`)

Dans l’approche Contract-First, la source de vérité est un **fichier OpenAPI** :

```
openapi: 3.0.3
info:
  title: ImmoService API
  version: 1.0.0

paths:
  /annonces:
    get:
      summary: Liste toutes les annonces
      responses:
        '200':
          description: OK
```

Ce fichier peut être écrit :

* **manuellement** (YAML),
* ou dans un éditeur dédié (Swagger Editor, Stoplight, Insomnia).

Le backend **implémente ensuite** ce contrat.

---

#### Exemples de stacks / workflows Contract-First

- **Express / Koa / Hapi (Node.js)**

   * Rédaction manuelle du `openapi.yaml`.
   * Servir la doc via `swagger-ui-express` (ou via page statique `public/docs`).
   * Génération des clients avec `openapi-generator` ou `swagger-codegen`.
   * *Pourquoi ?* Frameworks minimalistes, pas d’intégration automatique : le YAML est naturel.

   Exemple très simple (Express) :

   ```js
   import express from 'express';
   import swaggerUi from 'swagger-ui-express';
   import YAML from 'yamljs';

   const app = express();
   const spec = YAML.load('./openapi.yaml');

   app.use('/api/docs', swaggerUi.serve, swaggerUi.setup(spec));
   ```

-  **API Platform (Symfony / PHP)**

   * Permet contract-first via OpenAPI et exposes automatiquement la doc.
   * Très utilisé en enterprise où la spec circule entre équipes non-JS.

-  **Golang (gin / chi / echo)** ou services **Go**

   * Bonnes pratiques : fournir un `openapi.yaml` partagé, générer stubs/clients pour Go, TypeScript, etc.


- **Workflow “OpenAPI Generator”** (langage-agnostique)

   * Écrire `openapi.yaml` → `openapi-generator-cli` génère :

     * stubs serveur (Java, Node, Go…),
     * SDK client (TS, Java, Python…).
   * Très pratique pour des projets où l’on veut **prototyper l’API et générer le code** pour plusieurs langages.

- **gRPC / Protobuf** (éthique proche du contract-first)

   * Définition dans `.proto` (contrat), génération des serveurs/clients.
   * Utilisé quand la performance / le typage binaire sont prioritaires.


### Le cas **Next.js**



Contrairement à NestJS, FastAPI ou Spring, **Next.js n’intègre aucune solution native** pour documenter ses API.

Pour documenter une API construite avec Next.js, on a donc deux approches :

#### 1) Servir Swagger « à la main » (approche classique)

On écrit un fichier `openapi.yaml` séparé puis on expose :

* une route `/api/docs`
* une page (`"use client"`) avec un composant react qui charge Swagger UI et interprète le code de la route `/api/docs/`. Le composant `swagger-ui-react` fait cela.

C’est une approche **contract-first**, totalement indépendante du code.
Elle fonctionne bien, mais nécessite de **maintenir le YAML manuellement**.

#### 2) Utiliser un plugin code-first : *next-swagger-doc*

Pour se rapprocher du confort de frameworks code-first, il existe un plugin tiers :  **next-swagger-doc**

Il permet :

* d’ajouter des **annotations JSDoc** directement dans les handlers Next.js,
* de **générer automatiquement** la spécification OpenAPI,
* d’afficher une page Swagger UI intégrée à l’application (ex. `/api-doc`).

Cela transforme Next.js en une solution **code-first**, même si ce n’est pas nativement prévu dans le framework.


---

#### ✔️ Avantages du Contract-First

* Permet de **concevoir** l’API avant d’écrire du code.
* Idéal pour les équipes séparées front/back.
* Parfait pour générer des SDK client avant le backend.
* Facile à partager entre plusieurs langages ou services.

#### ❌ Limites

* Le fichier YAML peut **diverger** du code si on ne fait pas attention.
* Moins ergonomique (nombreuses répétitions).
* Pas de “vérification” automatique que le code respecte la spec.

