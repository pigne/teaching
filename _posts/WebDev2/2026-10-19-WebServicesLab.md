---
layout: post
title: Lab - Web Services
categories:
  - WebDev2
  - lab
author: Yoann Pigné
published: true
---

## ImmoService — une API pour le domaine immobilier

Au TP1, l'application était son propre et unique client. On coupe maintenant au
milieu : vous construisez un **service** destiné à des clients que vous ne
contrôlez pas.

Le livrable est une **API**, pas un site. Aucune interface n'est demandée.
Brancher un front dessus figure dans les bonus.

L'usage d'assistants IA est autorisé. L'évaluation porte donc sur des **cibles
mesurées**, sur votre **compréhension** à l'oral et sur le fichier `AI.md`.

Le barème distingue un **socle** et un **complément**. Un socle complet vaut
mieux que dix fonctionnalités à moitié faites : traitez-les dans l'ordre.

---

## Point de départ

Vous repartez de votre TP1, mais seulement de ses **données** :

- reprenez votre schéma Prisma et votre script de seed ;
- **nouveau projet, nouveau dépôt** pour le service ;
- votre application Next n'est ni modifiée ni requise.

### Adapter le schéma

Votre schéma contient les tables de Better Auth (`session`, `account`,
`verification`). Une API sans état n'en a pas besoin : l'authentification se
fera par JWT.

- gardez le domaine : `User`, `Annonce`, `Photo`, `Question`, `Reponse`, `Favori`
- remplacez les tables Better Auth par un champ `passwordHash` sur `User`
- gardez le champ `role`

Cette simplification est à justifier à l'oral.

Les mots de passe se hachent avec une bibliothèque éprouvée (`bcrypt`,
`argon2`) : jamais d'algorithme maison, jamais de stockage en clair.

### Les fichiers

Aucun upload n'est demandé. Une `Photo` est une **URL** et un texte alternatif
— par exemple `https://picsum.photos/seed/xxx/640/480`. L'upload figure dans
les bonus.

---

## Objectifs

- Concevoir un **contrat d'API** explicite, et le faire respecter par
  l'implémentation.
- Comparer **deux paradigmes** sur le même domaine.
- Sécuriser un service exposé : authentification, autorisation, durcissement.
- Mesurer le comportement : requêtes SQL, codes de statut, volume transféré.
- Tester à trois niveaux, dont des tests qui prouvent l'absence de faille.

---

## Contraintes

| Élément | Contrainte |
|---|---|
| Langage | TypeScript, `strict: true` |
| Framework | **libre** : NestJS, Fastify, Hono, Express + Zod, Elysia… |
| Base | PostgreSQL via Docker |
| ORM | Prisma 7.x |
| Validation | Zod ou équivalent, sur toute entrée |
| Auth | JWT, sans session en base |
| Tests | Vitest ou Jest, sur une base de test séparée |

Le choix du framework doit être justifiable. NestJS intègre OpenAPI
nativement ; Fastify et Hono sont plus légers et vous laissent assembler.

### Règles d'architecture

1. Aucun identifiant en dur : `docker-compose.yml` interpole depuis `.env`, et
   `.env.example` est réellement commité — attention au motif `.env*` dans
   `.gitignore`.
2. Chaque point d'entrée commence par : authentification → autorisation →
   validation. En REST **comme** dans un resolver GraphQL.
3. Aucun état applicatif en mémoire.
4. Les erreurs ont une forme unique dans toute l'API.
5. La politique d'autorisation est une fonction pure, sans dépendance au
   framework, donc testable sans serveur.

---

# Socle obligatoire

## Étape 1 — Le service et ses données

- [ ] Projet TypeScript qui démarre par `npm run dev`
- [ ] `docker-compose.yml` avec PostgreSQL, variables interpolées depuis `.env`
- [ ] Schéma Prisma adapté, migrations versionnées
- [ ] Seed : **deux agents distincts** au minimum, des `USER`, un `ADMIN`, des
      annonces réparties entre les deux agents dont des brouillons
- [ ] `.env.example` commité, sans secret réel

Les deux agents distincts conditionnent tous les tests d'autorisation de
l'étape 4.

## Étape 2 — API REST

L'API doit permettre :

- de consulter les annonces publiées — liste et détail ;
- à un agent, de gérer ses annonces : créer, modifier, supprimer, publier ou
  dépublier ;
- à un utilisateur authentifié, de poser une question sur une annonce ; et à
  l'agent propriétaire de cette annonce, d'y répondre ;
- de s'inscrire, de s'authentifier, et de récupérer son propre profil.

**Le découpage en ressources et le choix des URI et des verbes font partie du
travail.** Ils seront discutés à l'oral : pourquoi cette ressource plutôt qu'une
autre, pourquoi `PATCH` plutôt que `POST`, pourquoi une sous-ressource plutôt
qu'un champ, où placer la publication d'une annonce.

### Exigences

- [ ] Pagination avec métadonnée (`total`, `pages`)
- [ ] Filtres : au moins `ville`, `type`, `prixMax`
- [ ] Codes de statut corrects : `200` `201` `204` `400` `401` `403`/`404` `409`
- [ ] Erreurs de forme unique, avec un `code` stable et des détails par champ
- [ ] Body, query **et** path validés
- [ ] Un client qui poste `role=ADMIN` à l'inscription obtient un compte `USER`

Le choix entre `403` et `404` sur une ressource qui ne vous appartient pas est
à assumer : `403` en confirme l'existence.

## Étape 3 — Le contrat OpenAPI

- [ ] Spécification **OpenAPI 3.1** complète : paramètres, corps, réponses,
      codes d'erreur, schémas
- [ ] Approche code-first ou contract-first au choix, expliquée dans le `README`
- [ ] **Swagger UI servie** sur `/docs`
- [ ] Sécurité déclarée dans la spec (`bearerAuth`)

## Étape 4 — Authentification et autorisation

- [ ] `POST /auth/login` renvoie un JWT portant identifiant, rôle et expiration
- [ ] Vérification du jeton sur les routes protégées
- [ ] Rôles appliqués : `USER` / `AGENT` / `ADMIN`
- [ ] Un agent ne modifie ni ne supprime que ses propres annonces
- [ ] Un agent ne répond qu'aux questions posées sur ses annonces
- [ ] Les brouillons ne sont visibles que de leur auteur et des admins
- [ ] CORS configuré explicitement — pas `*` avec credentials

Test obligatoire : authentifiez-vous en agent A, récupérez l'`id` d'une annonce
de l'agent B, tentez de la supprimer. Si cela fonctionne, vous avez une faille
IDOR.

## Étape 5 — Tests

- [ ] Base de test séparée (`DATABASE_URL_TEST`) ; la suite **refuse de
      démarrer** si la variable manque
- [ ] Tests unitaires : politique d'autorisation, schémas de validation
- [ ] Tests E2E : login, routes protégées, CRUD, codes de statut
- [ ] Au moins un test prouvant l'absence d'IDOR, avec **deux** assertions : le
      code de statut et l'état de la base
- [ ] Les tests E2E consomment le **SDK généré** depuis votre OpenAPI

```bash
npx @openapitools/openapi-generator-cli generate \
  -i openapi.yaml -g typescript-fetch -o ./sdk
```

```ts
const api = new AnnoncesApi(new Configuration({ basePath: BASE, accessToken: jwt }));
const page = await api.listerAnnonces({ page: 1 });
```

Si la spec oublie un champ ou se trompe de type, le SDK ne compile pas et les
tests échouent : le contrat devient une dépendance vérifiée.

Pour vérifier que vos tests valent quelque chose, cassez volontairement le
filtre de propriété (`return {}`). Si la suite reste verte, ils ne testent rien.

---

# Complément

À traiter après le socle. Chaque point est indépendant.

## Étape 6 — API GraphQL

- [ ] Endpoint `/graphql` sur le même domaine
- [ ] Schéma : types, `enum`, types `input`, types de retour
- [ ] Queries : liste paginée et filtrable, détail par identifiant
- [ ] Mutations : créer, modifier, supprimer, publier/dépublier une annonce ;
      poser une question ; y répondre
- [ ] Les erreurs de validation sont dans le type de retour, pas dans les
      erreurs techniques
- [ ] Authentification par le contexte, autorisation **resolver par resolver**
- [ ] **DataLoader** sur au moins une relation (`Annonce.auteur`), avec un
      loader par requête HTTP
- [ ] Un garde-fou : limite de profondeur ou plafond de pagination serveur

> ⚠️ Il n'y a pas de middleware d'autorisation en GraphQL : un seul endpoint est
> exposé. Un resolver oublié est une porte ouverte, et aucun test de route ne le
> détecte.

## Étape 7 — Cache HTTP

- [ ] `Cache-Control` adapté selon les endpoints (`public` / `private` / `no-store`)
- [ ] `ETag` sur `GET /annonces`, et `304` sur `If-None-Match`

## Étape 8 — Durcissement

- [ ] Limitation de débit sur `/auth/login` → `429` au-delà du seuil
- [ ] `select` explicites : l'API ne renvoie jamais `passwordHash`
- [ ] Plafond serveur sur la pagination, quoi que demande le client

## Étape 9 — Audit de sécurité

Un fichier `SECURITY.md`, une page maximum :

- [ ] Quelles failles de l'OWASP API Top 10 avez-vous cherchées ?
- [ ] Lesquelles avez-vous trouvées dans votre code, et corrigées ?
- [ ] Où stockez-vous le JWT côté client, et pourquoi ?
- [ ] Que se passe-t-il si un JWT est volé ? Pouvez-vous le révoquer ?

---

# Bonus

- Brancher le front du TP1 sur l'API, via le SDK généré
- Upload de photos en `multipart`, documenté dans l'OpenAPI, avec plafond de
  taille, vérification du type réel et nom de fichier régénéré côté serveur
- Rafraîchissement de jeton avec rotation
- Souscriptions GraphQL
- Déploiement, avec URL accessible

---

## Critères mesurables

| Critère | Vérification |
|---|---|
| Le service démarre | `docker compose up -d`, migrate, seed, `npm run dev` sur machine vierge |
| Build | `npm run build` sans erreur TS |
| Tests | `npm test` et les tests E2E passent |
| Contrat utilisable | la spec valide et le SDK généré compile |
| Pas d'IDOR | agent A sur une annonce de B → `403`/`404`, donnée intacte |
| Rôle non falsifiable | `register` avec `role=ADMIN` → compte `USER` |
| Pas de fuite | aucune réponse ne contient `passwordHash` |
| Tests isolés | `npm test` ne touche pas la base de développement |
| *(compl.)* Cache | 2ᵉ requête avec `If-None-Match` → `304` |
| *(compl.)* Rate limiting | 15 appels sur `/auth/login` → `429` |
| *(compl.)* Pas de N+1 | requêtes SQL constantes sur une liste GraphQL imbriquée |

Le critère N+1 ne porte pas sur un nombre absolu mais sur l'**invariance** : une
liste de 2 et une liste de 20 annonces avec leur auteur doivent émettre le même
nombre de requêtes. Mesurez deux fois et comparez.

---

## Livrables

Projet en **binôme**, dans un nouveau dépôt privé sur la
[forge](https://www-apps.univ-lehavre.fr/forge/).

Dès le début, envoyez un mail à `yoann.pigne@univ-lehavre.fr`, objet
`[M2 IWOCS WEB] Projet n°2`, avec noms, numéros d'étudiant, logins et URL du
projet. Ajoutez-moi comme `developer`.

Le dépôt contient :

1. le code du service, structuré
2. `docker-compose.yml` et `.env.example`
3. les migrations et le script de seed
4. la spécification OpenAPI
5. les scripts npm : `dev`, `build`, `start`, `test`, `test:e2e`, `db:seed`, et
   la génération du SDK
6. un `README.md` : démarrage depuis zéro, comptes de test, choix du framework
   et de l'approche OpenAPI avec leurs justifications
7. `SECURITY.md` si l'étape 9 est traitée
8. `AI.md`

### `AI.md`

Une page :

1. ce que vous avez délégué, ce que vous avez écrit, et pourquoi ce partage ;
2. deux suggestions rejetées, avec la justification technique ;
3. un bug introduit par génération : lequel, comment détecté, comment corrigé.

Un `AI.md` complaisant est pénalisé ; un `AI.md` lucide est valorisé, y compris
s'il rapporte un échec.

### Travail en équipe

- [ ] Branches de fonctionnalités, intégration par *merge requests*
- [ ] Contributions visibles des deux membres dans l'historique

---

## Évaluation

Rendu avant le **dimanche 15 novembre 2026, 23h59**, délai impératif.
Oral le **lundi 16 novembre 2026**, en binôme, **20 minutes**.

Pas de présentation préparée : machine ouverte, sur votre dépôt. Les deux
membres doivent pouvoir répondre sur l'ensemble.

Exemples de questions :

- Où est vérifié qu'un agent est propriétaire de l'annonce qu'il supprime ? En
  REST, et en GraphQL.
- Combien de requêtes SQL pour cette liste GraphQL ? Et avec 20 annonces ?
- Qui peut appeler ce resolver ?
- Pourquoi n'avez-vous pas de table `session` ?
- Où stockez-vous le JWT, et que se passe-t-il s'il est volé ?
- CORS protège-t-il votre API ?
- Vous renvoyez `403` ou `404` ici : pourquoi ?
- Comment ajouteriez-vous une devise aux prix sans casser vos clients ?
- Que se passe-t-il si j'appelle `/annonces?limit=999999` ?
- Montrez-moi une réponse contenant `passwordHash`.

L'oral a lieu le lendemain du rendu : c'est l'état du dépôt au 15 novembre
23h59 qui est évalué, et le service doit démarrer sur une machine vierge.

Aptitudes évaluées : colonne « Web Services » du
[tableau des aptitudes](/teaching/WebDev2/#evaluations-et-aptitudes).
