---
layout: post
title: Web Services
categories:
  - WebDev2
  - lecture
author: Yoann Pigné
published: true
---

## Master 2 – Développement Web (Partie 2)
### Couper au milieu : le contrat entre un service et ses clients

---

## 0. Où en est-on

Au TP1, tout vivait dans un seul projet : le contrat entre les couches était
implicite, c'était du TypeScript qui s'appelait lui-même. Acquis : frontière
serveur / client, Server Actions, ORM typé, validation runtime, et la
distinction authentification / autorisation.

On coupe maintenant l'application en deux et on rend le contrat explicite :

```
Bloc 1 :  [ front + logique + données ]            un seul projet

Bloc 2 :  [ clients ]  ──contrat──▶  [ service ]  ──▶  données
           curl, tests,              REST + GraphQL
           mobile, autre service
```

Dès qu'il y a plus d'un consommateur, des questions nouvelles deviennent
centrales : comment décrire ce que je garantis, comment évoluer sans casser,
qui a le droit d'appeler, combien de données part sur le réseau.

Dans la chaîne de l'année, on est au milieu :

```
schéma → types → validation → [ CONTRAT ] → types du client → composant → pixels
```

---

## 1. REST

### Ressources et URI

REST manipule des **ressources** : des objets du domaine, identifiés par une
URI, manipulés par les verbes HTTP.

```
GET    /annonces           liste
GET    /annonces/42        une ressource
POST   /annonces           création
PATCH  /annonces/42        modification partielle
PUT    /annonces/42        remplacement complet
DELETE /annonces/42        suppression
```

Un nom au pluriel, pas de verbe dans l'URI : `/annonces/42` et non
`/getAnnonce?id=42`. Le verbe est déjà dans la méthode.

### Sans état

Chaque requête porte tout ce qu'il faut pour être traitée ; le serveur ne garde
pas de contexte entre deux appels. Conséquences : les réponses peuvent être
mises en cache (§ 5), on peut ajouter des instances derrière un répartiteur de
charge sans y coller un client, et le comportement est reproductible donc
testable.

C'est aussi ce qui explique le JWT plutôt qu'une session en base pour une API
(§ 4) : le jeton *est* le contexte, porté par le client.

### Codes de statut

| Code | Sens | Quand |
|---|---|---|
| `200` | OK | lecture réussie |
| `201` | Created | création, avec un en-tête `Location` |
| `204` | No Content | suppression réussie |
| `304` | Not Modified | le client a déjà la bonne version (§ 5) |
| `400` | Bad Request | échec de validation |
| `401` | Unauthorized | non authentifié |
| `403` | Forbidden | authentifié mais pas autorisé |
| `404` | Not Found | inexistant, ou dont l'existence ne doit pas être révélée |
| `409` | Conflict | violation de contrainte (email déjà pris) |
| `422` | Unprocessable | sémantiquement invalide |
| `429` | Too Many Requests | limitation de débit (§ 4) |

La confusion `401` / `403` est la plus fréquente : `401` relève de
l'authentification, `403` de l'autorisation.

Le choix entre `403` et `404` mérite réflexion. Si un agent demande une annonce
qui existe mais ne lui appartient pas, répondre `403` lui confirme qu'elle
existe. Sur des ressources sensibles, `404` ne révèle rien — c'est ce que fait
le `deleteMany` du bloc 1, qui ne distingue pas « absent » de « interdit ».

### Erreurs

Une API dont les erreurs changent de forme selon l'endroit est inexploitable.

```json
{
  "error": {
    "code": "VALIDATION_FAILED",
    "message": "La requête est invalide",
    "details": [
      { "field": "prix", "message": "doit être strictement positif" }
    ]
  }
}
```

Un `code` stable et machine-lisible, sur lequel le client peut réagir ; un
`message` pour les humains ; des `details` par champ. Ces codes font partie du
contrat : en changer un casse les clients qui les testaient.

### Pagination

Ne renvoyez jamais une collection non bornée.

```
GET /annonces?page=2&limit=20
```

```json
{
  "data": [ … ],
  "meta": { "page": 2, "limit": 20, "total": 137, "pages": 7 }
}
```

L'alternative est la pagination par curseur, pour les grandes collections et
les flux :

```
GET /annonces?limit=20&after=eyJpZCI6NDJ9
```

| | Décalage | Curseur |
|---|---|---|
| Lisibilité | `page=3` est évident | opaque |
| Aller directement page 7 | possible | impossible |
| Insertion pendant la pagination | doublons ou sauts | stable |
| Coût en base | `OFFSET 10000` est lent | indexé, constant |

Le défaut du décalage est réel : si une annonce est créée entre la page 1 et la
page 2, un élément déjà vu réapparaît.

### Filtres et tri

```
GET /annonces?ville=Le%20Havre&prixMax=250000&type=VENTE&sort=-createdAt
```

Ces paramètres viennent du réseau, donc ils se valident. Sans validation,
`?limit=999999` épuise la base, `?sort=champInexistant` produit un `500` là où
un `400` est attendu, et `?sort=passwordHash` laisse le client ordonner sur une
colonne non exposée. Avec un ORM paramétré, l'injection SQL n'est pas le risque
ici — elle le redevient dès qu'on écrit du SQL brut non paramétré.

---

## 2. Le contrat : OpenAPI

Une API sans description écrite n'est utilisable que par son auteur. **OpenAPI**
est la spécification standard pour la décrire ; *Swagger* désigne l'outillage.

Ce qu'elle apporte : une documentation interactive, un contrat permettant au
client de travailler avant que le serveur existe, la **génération de clients
typés**, la validation des requêtes et réponses, et des tests de conformité.

### Structure

```yaml
openapi: 3.1.0
info:
  title: ImmoService API
  version: 1.0.0

paths:
  /annonces:
    get:
      summary: Liste paginée des annonces publiées
      parameters:
        - name: page
          in: query
          schema: { type: integer, minimum: 1, default: 1 }
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema: { $ref: "#/components/schemas/AnnoncePage" }
        "400": { $ref: "#/components/responses/Invalide" }

components:
  schemas:
    Annonce:
      type: object
      required: [id, titre, prix, ville]
      properties:
        id: { type: integer }
        titre: { type: string }
        prix: { type: number }
        ville: { type: string }
```

Trois parties couvrent l'essentiel : `paths` pour les endpoints,
`components/schemas` pour les modèles, `components/responses` et `requestBody`
pour les formes d'échange réutilisables.

### Les trois formes de paramètres

```yaml
# query  →  GET /annonces?prixMax=150000
- name: prixMax
  in: query
  schema: { type: integer }

# path   →  GET /annonces/42
- name: id
  in: path
  required: true
  schema: { type: integer }

# body   →  POST /annonces
requestBody:
  required: true
  content:
    application/json:
      schema: { $ref: "#/components/schemas/CreerAnnonce" }
```

### Code-first ou contract-first

Le choix détermine **qui est la source de vérité**.

| | Code-first | Contract-first |
|---|---|---|
| Source de vérité | le code | le fichier `openapi.yaml` |
| Production de la spec | générée depuis décorateurs ou types | écrite à la main |
| Risque principal | la spec suit le code, même mal conçu | dérive entre spec et implémentation |
| Concevoir avant de coder | difficile | naturel |
| Équipes séparées front / back | moins pratique | le contrat circule |
| Documentation à jour | par construction | si l'on est discipliné |

Code-first, avec NestJS :

```ts
@ApiTags("annonces")
@Controller("annonces")
export class AnnoncesController {
  @Get()
  @ApiOperation({ summary: "Liste paginée des annonces publiées" })
  @ApiOkResponse({ type: AnnoncePageDto })
  findAll(@Query() q: ListerAnnoncesDto) {
    return this.service.findAll(q);
  }
}
```

NestJS produit alors `/api-json` et `/api`, sans aucun YAML. Même approche avec
FastAPI (Python), ASP.NET Core ou Spring Boot.

Contract-first : on écrit le YAML, puis on implémente — ou on génère les
squelettes avec `openapi-generator`.

Next.js n'a aucun support natif d'OpenAPI : documenter une API faite en Route
Handlers impose d'écrire le YAML à la main ou de passer par un plugin tiers.
C'est une des raisons pour lesquelles un service dédié est plus confortable.

### Servir la documentation

```ts
const config = new DocumentBuilder()
  .setTitle("ImmoService API")
  .setVersion("1.0")
  .addBearerAuth()
  .build();

SwaggerModule.setup("docs", app, SwaggerModule.createDocument(app, config));
```

### Générer un client typé

```bash
openapi-generator-cli generate -i openapi.yaml -g typescript-fetch -o ./sdk
```

```ts
import { AnnoncesApi, Configuration } from "./sdk";

const api = new AnnoncesApi(new Configuration({ basePath: BASE, accessToken: jwt }));
const page = await api.listerAnnonces({ page: 1, ville: "Le Havre" });
//    ^? AnnoncePage, dérivé de la spec
```

Utilisez ce SDK **dans vos tests end-to-end** plutôt que des `fetch` manuels :
un champ oublié dans la spec n'est pas exposé par le SDK, un type faux provoque
une erreur de compilation, un endpoint renommé sans mise à jour disparaît. Le
contrat cesse d'être un document pour devenir une dépendance vérifiée.

### Limites

OpenAPI devient verbeux dès qu'une API grossit, ne permet pas au client de
choisir les champs renvoyés, et son versionnement se complique quand des
modèles sont partagés entre endpoints (§ 6). C'est là que GraphQL propose autre
chose.

---

## 3. GraphQL

### Le principe

Un endpoint unique, et c'est le **client** qui décrit ce qu'il veut :

```graphql
query {
  annonces(page: 1) {
    data {
      id
      titre
      prix
      auteur { name }        # relation suivie dans la même requête
    }
  }
}
```

Le serveur répond exactement cette forme. Deux écrans différents donnent deux
requêtes différentes, sur un seul endpoint, sans nouvelle route à écrire.

### Le schéma

Ici le contrat n'est pas un fichier séparé : c'est le schéma, et il est
obligatoire.

```graphql
enum TypeBien { VENTE LOCATION }
enum StatutBien { DISPONIBLE LOUE VENDU }

type Utilisateur {
  id: ID!
  name: String!
  annonces: [Annonce!]!
}

type Annonce {
  id: ID!
  titre: String!
  description: String!
  prix: Float!
  ville: String!
  surface: Int
  type: TypeBien!
  statutBien: StatutBien!
  auteur: Utilisateur!
  questions: [Question!]!
}

type Question {
  id: ID!
  contenu: String!
  auteur: Utilisateur!
  reponse: Reponse
}
```

`!` signifie *non nul*. `[Annonce!]!` est une liste non nulle d'éléments non
nuls, à distinguer de `[Annonce!]` (la liste peut être `null`) et de
`[Annonce]!` (la liste existe mais peut contenir des `null`). Cette précision
fait partie du contrat.

### Queries

```graphql
type AnnoncePage {
  data: [Annonce!]!
  total: Int!
  pages: Int!
}

type Query {
  annonces(page: Int = 1, ville: String, prixMax: Float): AnnoncePage!
  annonce(id: ID!): Annonce
}
```

`annonce(id: ID!): Annonce` sans `!` sur le retour : un identifiant inconnu
renvoie `null`, ce n'est pas une erreur.

### Mutations

Trois règles de conception évitent beaucoup de difficultés.

**Un type `input` dédié** plutôt qu'une liste d'arguments :

```graphql
input CreerAnnonceInput {
  titre: String!
  description: String!
  prix: Float!
  ville: String!
  type: TypeBien!
  surface: Int
}
```

Avec dix arguments en vrac, chaque ajout de champ modifie la signature ; un
`input` évolue sans rien casser.

**Un type de retour dédié**, portant aussi les erreurs métier :

```graphql
type ErreurChamp {
  champ: String!
  message: String!
}

type ResultatAnnonce {
  annonce: Annonce
  erreurs: [ErreurChamp!]!
}

type Mutation {
  creerAnnonce(input: CreerAnnonceInput!): ResultatAnnonce!
  modifierAnnonce(id: ID!, input: ModifierAnnonceInput!): ResultatAnnonce!
  supprimerAnnonce(id: ID!): Boolean!
  publierAnnonce(id: ID!, publiee: Boolean!): ResultatAnnonce!
}
```

Une erreur de validation n'est pas une erreur technique : elle fait partie du
résultat normal. Dans le type de retour, le client la reçoit typée au lieu de
fouiller un tableau `errors` générique.

**Les mutations sont aussi publiques qu'une route REST**, et soumises aux mêmes
règles que celles du bloc 1.

### Resolvers

Un resolver dit comment obtenir un champ. Signature :
`(parent, args, context, info)`.

```ts
export const resolvers = {
  Query: {
    annonces: async (_p, args, ctx) => {
      const q = ListeQuery.parse(args);
      const where = { statut: "PUBLIE" as const, ...filtres(q) };
      const [data, total] = await Promise.all([
        ctx.prisma.annonce.findMany({ where, skip: (q.page - 1) * 20, take: 20 }),
        ctx.prisma.annonce.count({ where }),
      ]);
      return { data, total, pages: Math.ceil(total / 20) };
    },

    annonce: (_p, { id }, ctx) =>
      ctx.prisma.annonce.findFirst({
        where: { id: IdParam.parse(id), statut: "PUBLIE" },
      }),
  },

  Mutation: {
    creerAnnonce: async (_p, { input }, ctx) => {
      const user = exigerRole(ctx, "AGENT", "ADMIN");    // auth + autorisation

      const parsed = CreerAnnonce.safeParse(input);       // validation
      if (!parsed.success) {
        return { annonce: null, erreurs: enErreursChamps(parsed.error) };
      }

      const annonce = await ctx.prisma.annonce.create({
        data: { ...parsed.data, auteurId: user.id },      // auteur pris du contexte
      });
      return { annonce, erreurs: [] };
    },

    supprimerAnnonce: async (_p, { id }, ctx) => {
      const user = exigerRole(ctx, "AGENT", "ADMIN");

      const res = await ctx.prisma.annonce.deleteMany({
        where: { id: IdParam.parse(id), ...filtreProprietaire(user) },
      });
      return res.count === 1;
    },
  },

  Annonce: {
    auteur: (annonce, _a, ctx) => ctx.chargeurs.utilisateur.load(annonce.auteurId),
  },
};
```

Le même trio qu'au bloc 1 — authentification, autorisation, validation — en
REST comme en GraphQL, dans une Server Action comme dans un resolver.

### Le contexte

Construit une fois par requête et passé à tous les resolvers, il porte
l'identité de l'appelant.

```ts
await startStandaloneServer(server, {
  context: async ({ req }) => {
    const token = req.headers.authorization?.replace("Bearer ", "");
    return {
      prisma,
      user: token ? verifierJwt(token) : null,
      chargeurs: creerChargeurs(prisma),
    };
  },
});
```

> ⚠️ Il n'existe pas de middleware d'autorisation en GraphQL. En REST, un
> middleware protège `/annonces/*` d'un coup ; ici un **seul endpoint** est
> exposé, tout passe par `POST /graphql`. L'autorisation doit donc être
> vérifiée resolver par resolver, et un resolver oublié n'est détecté par aucun
> test de route.

### Le problème N+1 et DataLoader

C'est le piège principal de GraphQL, et il est plus sévère qu'en REST parce que
c'est le client qui décide de la profondeur.

```graphql
query {
  annonces { data { titre auteur { name } } }   # 20 annonces
}
```

Le resolver de champ `Annonce.auteur` est appelé une fois par annonce. En
version naïve :

```ts
Annonce: {
  auteur: (a, _, ctx) => ctx.prisma.user.findUnique({ where: { id: a.auteurId } }),
}
```

1 requête pour les annonces, 20 pour les auteurs : 21 au total. Avec 500
annonces, 501 — et le bloc 1 l'a montré, c'est invisible sur un jeu de test de
trois lignes.

**DataLoader** regroupe les appels d'un même tour de boucle d'événements en une
seule requête :

```ts
import DataLoader from "dataloader";

export function creerChargeurs(prisma: PrismaClient) {
  return {
    utilisateur: new DataLoader<string, User>(async (ids) => {
      const users = await prisma.user.findMany({
        where: { id: { in: [...ids] } },
        select: { id: true, name: true },
      });
      const parId = new Map(users.map((u) => [u.id, u]));
      return ids.map((id) => parId.get(id)!);   // ordre des ids demandés
    }),
  };
}
```

Deux requêtes, quel que soit le nombre d'annonces. Deux pièges :

- **un DataLoader par requête HTTP**, jamais un singleton partagé : il met en
  cache, donc un loader global servirait des données périmées, et croisées
  entre utilisateurs ;
- **l'ordre du retour** doit correspondre exactement à celui des `ids` reçus.
  Une erreur ici associe silencieusement le mauvais auteur à la mauvaise
  annonce.

Comme au bloc 1, le bon critère n'est pas le nombre de requêtes mais son
**invariance** : mesurez avec 2 puis 20 éléments et comparez.

### Garde-fous

Le revers de la flexibilité : un client peut écrire une requête très coûteuse.

```graphql
query Abus {
  annonces { data { auteur { annonces { data { auteur { annonces {
    data { titre }
  } } } } } } }
}
```

Protections usuelles : limiter la profondeur (`graphql-depth-limit`), établir
un budget de complexité, plafonner la pagination côté serveur
(`take: Math.min(limit, 100)`), désactiver l'introspection en production si
l'API n'est pas publique, n'accepter que des requêtes persistées.

Une API REST n'a pas ce problème : c'est le serveur qui décide de la forme de
chaque réponse. C'est le prix de la flexibilité.

### REST ou GraphQL

| Critère | REST | GraphQL |
|---|---|---|
| Nombre d'endpoints | plusieurs | un |
| Qui choisit les champs | le serveur | le client |
| Sur-récupération | fréquente | évitée |
| Allers-retours pour des données liées | plusieurs | un |
| Cache HTTP | natif (§ 5) | inopérant |
| Codes de statut | expressifs | `200` presque toujours |
| Upload de fichiers | naturel | `multipart` non standard ou base64 |
| Risque principal | sur-récupération, multiplication des routes | N+1, requêtes abusives |
| Outillage de contrat | OpenAPI | schéma et introspection natifs |

En pratique : REST pour une API publique, cacheable, largement consommée ;
GraphQL pour des interfaces riches dont les besoins varient d'un écran à
l'autre. Les deux coexistent sans difficulté.

---

## 4. Sécurité d'une API

L'API est exposée au réseau, sans formulaire ni bouton grisé pour guider son
usage : tout ce qui n'est pas vérifié est permis.

### Le JWT

Un *JSON Web Token* est un jeton **signé**, non chiffré, en trois parties :

```
eyJhbGciOiJIUzI1NiJ9 . eyJzdWIiOiI0MiIsInJvbGUiOiJBR0VOVCJ9 . 3xK9…
└─── en-tête ────────┘ └─── charge utile ──────────────────┘ └ signature ┘
```

```json
{ "sub": "cmu1p…", "role": "AGENT", "iat": 1763…, "exp": 1763… }
```

La charge utile **n'est pas secrète** : elle est encodée en base64 et lisible
par quiconque. La signature garantit qu'elle n'a pas été modifiée, pas qu'elle
est confidentielle. N'y placez aucune donnée sensible.

Vérifier consiste à recalculer la signature avec le secret, puis contrôler
`exp`. Si la signature est valide, le `role` qu'il contient est digne de
confiance — c'est ce qui rend l'API sans état.

#### Où le client le stocke

| | `localStorage` | cookie `httpOnly` |
|---|---|---|
| Lisible par JavaScript | oui, donc volable par une XSS | non |
| Envoyé automatiquement | non | oui |
| Vulnérable au CSRF | non | oui, sans précaution |
| Client non navigateur | simple | sans objet |

Il n'y a pas de réponse universelle. Pour une API consommée par des clients non
navigateurs — scripts, mobile, tests — l'en-tête `Authorization: Bearer …` est
l'usage normal.

#### Expiration et rafraîchissement

Un JWT ne peut pas être révoqué : il est valide jusqu'à `exp`. D'où une durée
de vie courte pour le jeton d'accès (15 min à 1 h), un *refresh token* à durée
longue stocké en base donc révocable, et une rotation à chaque
rafraîchissement. C'est le prix du sans-état, et le miroir du choix « session en
base ou JWT » du bloc 1.

### Autorisation : l'IDOR

La faille du bloc 1 revient à l'identique, simplement exposée plus largement.

```ts
// Authentifié ≠ autorisé
app.delete("/annonces/:id", requireAuth, async (req, res) => {
  await prisma.annonce.delete({ where: { id: Number(req.params.id) } });
  res.status(204).send();
});
```

Avec un JWT valide, n'importe qui supprime n'importe quoi en changeant l'`id`.

```ts
const res = await prisma.annonce.deleteMany({
  where: { id, ...filtreProprietaire(req.user) },
});
if (res.count === 0) return reply.code(404).send(erreur("NOT_FOUND"));
```

La seule méthode de vérification fiable reste le test avec deux comptes
distincts.

### OWASP API Security

| Risque | En une phrase | Parade |
|---|---|---|
| Autorisation au niveau objet (IDOR) | changer un `id` donne accès à autre chose | propriété dans le `where` |
| Authentification défaillante | jeton sans expiration, login sans limite | `exp` court, limitation de débit |
| Exposition excessive de données | l'API renvoie tout, le client filtre | `select` explicite côté serveur |
| Autorisation au niveau fonction | `DELETE` accessible à un rôle non prévu | vérifier le rôle sur chaque opération |
| Consommation de ressources | pagination non plafonnée, requête abusive | plafonds serveur, limite de profondeur |

L'exposition excessive est insidieuse : `findMany()` sans `select` renvoie
toutes les colonnes, y compris celles ajoutées plus tard. Un champ
`notesInternes` créé un jour part dans l'API le même jour, sans avertissement.
Un `select` explicite est une liste blanche.

### CORS

Le navigateur empêche une page servie par `a.fr` de lire une réponse de `b.fr`,
sauf si `b.fr` l'autorise explicitement.

```ts
app.use(cors({
  origin: ["https://immo.univ-lehavre.fr"],   // pas "*" avec credentials
  methods: ["GET", "POST", "PATCH", "DELETE"],
  credentials: true,
}));
```

Pour les requêtes non simples, le navigateur envoie d'abord un préliminaire
`OPTIONS` ; si le serveur n'y répond pas correctement, la vraie requête n'est
jamais émise.

> ⚠️ CORS n'est **pas** une protection du serveur : c'est une règle appliquée
> par le navigateur. `curl`, un script Node ou un client mobile l'ignorent
> entièrement. Ce qui protège le serveur, c'est l'authentification et
> l'autorisation.

### Limitation de débit

Sans limite, `/auth/login` accepte un nombre illimité de tentatives : c'est une
invitation à l'attaque par force brute et au *credential stuffing* (rejouer des
couples issus de fuites d'autres sites).

```ts
app.use("/auth/login", rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 10,
  message: { error: { code: "TOO_MANY_REQUESTS" } },
}));
```

Au-delà, `429`, idéalement avec un en-tête `Retry-After`. Vérifiable en une
ligne :

```bash
for i in $(seq 1 15); do curl -s -o /dev/null -w '%{http_code} ' \
  -X POST localhost:3000/auth/login -d '{"email":"x","password":"y"}'; done
# 401 401 401 … 429 429 429
```

---

## 5. Cache HTTP

C'est l'avantage structurel de REST, et il est presque toujours laissé de côté.

### `Cache-Control`

```http
Cache-Control: public, max-age=300              # cacheable 5 min, par tous
Cache-Control: private, max-age=60              # par le client seulement
Cache-Control: no-store                         # jamais
```

`public` autorise les caches partagés (CDN, proxy), `private` limite au cache
navigateur, `max-age` fixe la fraîcheur en secondes, `no-store` interdit tout,
`must-revalidate` impose une revalidation une fois le contenu périmé.

Une liste d'annonces publiques est `public` ; une réponse contenant des données
d'un utilisateur authentifié est `private`, voire `no-store`.

### `ETag` et revalidation

`max-age` dit « ne redemande pas avant N secondes ». L'`ETag` répond à la
question suivante : est-ce que cela a changé ?

```http
GET /annonces
→ 200 OK
  ETag: "a1b2c3"
  Cache-Control: public, max-age=60
  [ 24 ko de JSON ]
```

```http
GET /annonces
  If-None-Match: "a1b2c3"
→ 304 Not Modified
  [ corps vide ]
```

Le serveur a répondu sans transférer les données. Sur une liste consultée en
boucle, c'est la différence entre 24 ko et quelques octets d'en-têtes.

```ts
import { createHash } from "node:crypto";

const data = await listerAnnonces(q);
const etag = `"${createHash("sha1").update(JSON.stringify(data)).digest("hex")}"`;

if (req.headers["if-none-match"] === etag) {
  return reply.code(304).send();
}
reply.header("ETag", etag)
     .header("Cache-Control", "public, max-age=60")
     .send(data);
```

```bash
ETAG=$(curl -sI localhost:3000/annonces | grep -i etag | cut -d' ' -f2 | tr -d '\r')
curl -s -o /dev/null -w '%{http_code}\n' -H "If-None-Match: $ETAG" \
     localhost:3000/annonces
# → 304
```

La partie 3 sera évaluée sur le volume de données transféré. Le cache HTTP en
est le premier levier : ne pas renvoyer ce que le client possède déjà.

### Et GraphQL

Un seul endpoint, tout en `POST`, un corps différent à chaque appel : le cache
HTTP ne s'applique pas. On le remplace par du cache applicatif — cache de
resolver, cache normalisé côté client, requêtes persistées en `GET`. C'est un
coût réel de GraphQL, rarement mentionné dans les comparatifs.

---

## 6. Faire évoluer une API

Dès qu'un client dépend de vous, vous ne pouvez plus changer librement.

Sont **compatibles** : ajouter un endpoint, ajouter un champ optionnel en
entrée, ajouter un champ en sortie.

Sont **cassants** : renommer ou supprimer un champ, rendre un champ
obligatoire, changer un type, changer la forme d'une erreur, resserrer une
validation.

```
/v1/annonces   et   /v2/annonces        version dans l'URI
Accept: application/vnd.immo.v2+json    négociation de contenu
```

En GraphQL on ne versionne généralement pas : on ajoute, et on marque l'ancien
comme obsolète.

```graphql
type Annonce {
  prix: Float! @deprecated(reason: "Utiliser prixTTC depuis 2026-11")
  prixTTC: Float!
}
```

Le cycle habituel : déprécier, mesurer qui utilise encore, puis supprimer. Sans
mesure, rien n'est jamais supprimé.

---

## 7. Tester un service

Une API est plus facile à tester qu'une application avec interface : les
entrées et les sorties sont du JSON, il n'y a pas de DOM à simuler.

| Niveau | Objet | Réseau |
|---|---|---|
| Unitaire | règles métier pures, schémas de validation | non |
| Intégration | une couche avec sa base réelle | non |
| End-to-end | le service complet, par HTTP | oui |

### Unitaire

Le bloc 1 a montré pourquoi la politique d'autorisation doit être une fonction
sans dépendance au framework : elle devient testable sans serveur, sans session
et sans base.

```ts
it("restreint un agent à ses propres annonces", () => {
  expect(filtreProprietaire({ id: "a1", role: "AGENT" }))
    .toEqual({ auteurId: "a1" });
});
```

### End-to-end, avec le SDK généré

```ts
const api = new AnnoncesApi(new Configuration({ basePath: BASE, accessToken: jwt }));

it("liste les annonces publiées, paginées", async () => {
  const page = await api.listerAnnonces({ page: 1 });
  expect(page.meta.page).toBe(1);
  expect(page.data.every((a) => a.statut === "PUBLIE")).toBe(true);
});
```

Le test ne compile pas si la spec ne décrit pas `meta.page` ou `data` : le
contrat est vérifié avant même l'exécution.

### Le test à deux comptes

```ts
it("un agent ne peut pas supprimer l'annonce d'un autre agent", async () => {
  const jwtA = await connecter("agent.a@immo.fr");
  const idDeB = await idAnnonceDe("agent.b@immo.fr");

  const res = await fetch(`${BASE}/annonces/${idDeB}`, {
    method: "DELETE",
    headers: { Authorization: `Bearer ${jwtA}` },
  });

  expect(res.status).toBe(404);
  expect(await existeEncore(idDeB)).toBe(true);
});
```

Deux assertions, pas une : le code de statut **et** l'état de la base. Un `404`
renvoyé après avoir quand même supprimé la ligne resterait une faille.

### GraphQL

Un `POST` sur `/graphql`, sans particularité.

```ts
it("refuse une mutation à un utilisateur sans rôle", async () => {
  const res = await graphql(
    `mutation { creerAnnonce(input: { titre: "x", prix: 1, ville: "y",
                                      description: "zzzzzzzzzz", type: VENTE })
       { annonce { id } erreurs { champ message } } }`,
    { jwt: jwtUtilisateurSimple },
  );
  expect(res.data.creerAnnonce.annonce).toBeNull();
});
```

Et le test de N+1, qui est une mesure plutôt qu'une assertion de valeur :

```ts
it("le nombre de requêtes SQL ne dépend pas du nombre d'annonces", async () => {
  const avec2  = await compterRequetes(() => requeteAvecAuteurs(2));
  const avec20 = await compterRequetes(() => requeteAvecAuteurs(20));
  expect(avec20).toBe(avec2);
});
```

### Base de test séparée

Les tests font table rase avant de s'exécuter. Sans base dédiée, ils effacent
les données de développement, et le symptôme est déroutant : plus aucun compte
ne fonctionne, et l'on soupçonne l'authentification.

```sh
DATABASE_URL="postgresql://…/immo"
DATABASE_URL_TEST="postgresql://…/immo_test"
```

Faites échouer la suite si la variable manque, plutôt que de retomber
silencieusement sur la base de développement.

### Vérifier les tests eux-mêmes

```ts
export function filtreProprietaire(user: Utilisateur) {
  return {};               // faille introduite volontairement
}
```

Si la suite reste verte, les tests d'autorisation ne testent rien.

---

## 8. Les autres paradigmes

**SOAP** — ancien standard industriel, XML et WSDL, expose des opérations
plutôt que des ressources. Rigoureux, sécurité avancée (WS-Security), encore
présent en banque et santé. Verbeux et mal adapté au Web moderne.

**gRPC** — communication binaire (Protocol Buffers) sur HTTP/2, contrat dans un
fichier `.proto`, génération de clients et serveurs, streaming bidirectionnel.
Excellent entre microservices internes, inutilisable depuis un navigateur sans
passerelle gRPC-Web.

```proto
service AnnonceService {
  rpc GetAnnonce (AnnonceRequest) returns (AnnonceResponse);
}
```

**tRPC** — spécifique à TypeScript : ni schéma ni génération de code, le client
importe le *type* du routeur serveur et obtient l'autocomplétion de bout en
bout.

```ts
const annonces = await trpc.annonces.lister.query({ page: 1 });
//    ^? déduit directement du code serveur
```

Confortable à condition que client et serveur vivent dans le même dépôt
TypeScript. Dès qu'un consommateur ne l'est pas, il faut un contrat explicite :
retour à OpenAPI ou GraphQL.

| Critère | REST | GraphQL | tRPC | gRPC | SOAP |
|---|---|---|---|---|---|
| Modèle | ressources | schéma + requêtes | procédures typées | RPC | opérations |
| Format | JSON | JSON | JSON | Protobuf | XML |
| Endpoints | plusieurs | un | un | plusieurs | plusieurs |
| Contrat | OpenAPI (externe) | schéma (intégré) | les types TS | `.proto` | WSDL |
| Typage | faible sans spec | fort | fort, TS seulement | très fort | très fort |
| Cache HTTP | oui | non | non | non | non |
| Clients hétérogènes | oui | oui | non | oui | oui |
| Navigateur | oui | oui | oui | passerelle requise | oui |
| Apprentissage | facile | moyen | facile | élevé | élevé |

---

## 9. Récapitulatif

| Question | Réponse |
|---|---|
| Qu'est-ce que je livre ? | un contrat, dont une implémentation |
| `401` ou `403` ? | non authentifié / authentifié sans droit |
| `403` ou `404` ? | `404` si révéler l'existence est déjà une fuite |
| Où valider les entrées ? | body, query, path, arguments GraphQL |
| Qui peut appeler mon resolver ? | n'importe qui : auth, autorisation, validation |
| Pourquoi pas de middleware en GraphQL ? | un seul endpoint, donc contrôle par resolver |
| Comment détecter un N+1 ? | mesurer avec 2 puis 20 éléments et comparer |
| CORS protège-t-il mon API ? | non, c'est une règle du navigateur |
| Puis-je révoquer un JWT ? | non : `exp` court et refresh token en base |
| Comment ne rien transférer d'inutile ? | `ETag` et `304`, `select` explicite |
| Comment évoluer sans casser ? | ajouter, déprécier, mesurer, supprimer |
| Mes tests valent-ils quelque chose ? | casser le code exprès et les voir échouer |

---

## Ressources

- OpenAPI → <https://spec.openapis.org/oas/latest.html>
- Swagger Editor → <https://editor.swagger.io/>
- OpenAPI Generator → <https://openapi-generator.tech/>
- GraphQL → <https://graphql.org/learn/>
- DataLoader → <https://github.com/graphql/dataloader>
- OWASP API Security Top 10 → <https://owasp.org/API-Security/>
- MDN — HTTP caching → <https://developer.mozilla.org/docs/Web/HTTP/Caching>
- MDN — CORS → <https://developer.mozilla.org/docs/Web/HTTP/CORS>
- JWT → <https://jwt.io/introduction>
