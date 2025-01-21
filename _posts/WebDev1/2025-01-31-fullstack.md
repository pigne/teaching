---
layout: post
title: Développement Full-Stack avec Node.js
categories:
- WebDev1
- lecture
author: Yoann Pigné
published: false
---

### Serveurs Back-End

Node.js permet de créer des applications serveur performantes et scalables avec JavaScript/TypeScript. Lorsqu'il est associé à des frameworks comme **Express.js**, il devient encore plus puissant pour construire des API web et des services back-end.

#### Introduction à Express.js

**Express.js** est un framework minimaliste et flexible pour Node.js qui facilite la création de serveurs web et d'API.

- Pour installer Express.js avec TypeScript :

  ```bash
  npm install express @types/express
  ```

- Exemple de serveur de base avec Express.js :

  ```ts
  import express from 'express';

  const app = express();
  const port = 3000;

  app.get('/', (req, res) => {
    res.send('Hello World!');
  });

  app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
  });
  ```

- **Middleware** : Utiliser des middlewares pour gérer des fonctionnalités comme le parsing des données JSON, la gestion des erreurs ou les autorisations.

  Exemple avec le middleware `body-parser` pour gérer les données JSON :

  ```ts
  import bodyParser from 'body-parser';
  
  app.use(bodyParser.json());
  ```

#### Création d'API RESTful avec TypeScript

Un service back-end moderne repose souvent sur la création d'APIs RESTful qui permettent de communiquer avec les clients via des requêtes HTTP.

Voici un exemple de création d'une API RESTful pour gérer un simple "To-Do List".

```ts
interface Todo {
  id: number;
  task: string;
  done: boolean;
}

const todos: Todo[] = [];

app.get('/todos', (req, res) => {
  res.json(todos);
});

app.post('/todos', (req, res) => {
  const newTodo: Todo = { id: todos.length + 1, task: req.body.task, done: false };
  todos.push(newTodo);
  res.status(201).json(newTodo);
});

app.put('/todos/:id', (req, res) => {
  const todo = todos.find(t => t.id === parseInt(req.params.id));
  if (!todo) return res.status(404).send('Todo not found');
  
  todo.done = req.body.done;
  res.json(todo);
});

app.delete('/todos/:id', (req, res) => {
  const index = todos.findIndex(t => t.id === parseInt(req.params.id));
  if (index === -1) return res.status(404).send('Todo not found');
  
  todos.splice(index, 1);
  res.status(204).send();
});
```

:exclamation: **Conseil** : Utiliser un ORM/ODM pour faciliter la gestion des données et éviter l'usage direct de SQL ou des requêtes non sécurisées.

---

### Bases de Données

Les bases de données sont au cœur de tout développement back-end. Que tu utilises une base de données relationnelle ou non relationnelle, Node.js et TypeScript offrent des solutions modernes pour interagir avec ces systèmes.

#### Utilisation de Prisma pour la gestion de la base de données

**Prisma** est un ORM moderne pour Node.js/TypeScript qui facilite la gestion des bases de données relationnelles comme PostgreSQL, MySQL, SQLite, etc. Il permet de travailler avec les données en utilisant des objets et des requêtes type-safe.

- Pour installer Prisma :

  ```bash
  npm install @prisma/client
  npx prisma init
  ```

- Exemple de schéma Prisma pour une base de données relationnelle :

  `schema.prisma` :

  ```prisma
  datasource db {
    provider = "postgresql"
    url      = env("DATABASE_URL")
  }

  model Todo {
    id    Int    @id @default(autoincrement())
    task  String
    done  Boolean
  }
  ```

- Exécution des migrations et génération des modèles :

  ```bash
  npx prisma migrate dev --name init
  ```

- Exemple d'utilisation de Prisma pour interagir avec la base de données :

  ```ts
  import { PrismaClient } from '@prisma/client';

  const prisma = new PrismaClient();

  async function main() {
    // Créer une tâche
    const todo = await prisma.todo.create({
      data: {
        task: 'Learn Prisma',
        done: false,
      },
    });

    console.log(todo);
  }

  main()
    .catch(e => {
      throw e;
    })
    .finally(async () => {
      await prisma.$disconnect();
    });
  ```

:six: **Avantage de Prisma** : Prisma te permet de travailler avec des types générés automatiquement, ce qui réduit les erreurs et améliore l’autocomplétion dans les éditeurs de code.

#### Introduction à MongoDB ou PostgreSQL

- **MongoDB** est une base de données NoSQL qui stocke des données sous forme de documents BSON. Pour l’utiliser avec Node.js, tu peux utiliser **Mongoose** comme ODM.
  
  Exemple d’installation de Mongoose pour MongoDB :

  ```bash
  npm install mongoose
  ```

- **PostgreSQL** est une base de données relationnelle populaire et robuste, bien adaptée pour des applications complexes. Elle peut être utilisée avec Prisma (comme montré précédemment) ou avec d'autres ORMs comme TypeORM.

---

### Sécurité et Bonnes Pratiques

La sécurité est une priorité absolue dans le développement back-end. Il est important de protéger les données et d'éviter les vulnérabilités qui peuvent être exploitées par des attaquants.

#### Gestion des JWT

**JSON Web Tokens (JWT)** est un standard ouvert qui permet de transmettre des informations sécurisées entre les parties sous forme d'un objet JSON signé.

- Pour utiliser JWT avec Node.js, tu peux installer la bibliothèque `jsonwebtoken` :

  ```bash
  npm install jsonwebtoken
  ```

- Exemple de génération et de vérification d’un JWT :

  ```ts
  import jwt from 'jsonwebtoken';

  const secretKey = 'mysecretkey';

  // Génération du token
  const token = jwt.sign({ userId: 123 }, secretKey, { expiresIn: '1h' });

  console.log('Token:', token);

  // Vérification du token
  jwt.verify(token, secretKey, (err, decoded) => {
    if (err) {
      console.error('Invalid Token');
    } else {
      console.log('Decoded:', decoded);
    }
  });
  ```

:six: **Conseil** : Stocke toujours ton secret de manière sécurisée et ne le laisse pas en clair dans le code.

#### Sécurisation des requêtes (CORS, Rate Limiting)

- **CORS** (Cross-Origin Resource Sharing) : Permet de contrôler quelles ressources peuvent être accessibles depuis d'autres domaines. Avec Express.js, tu peux utiliser le middleware `cors` pour activer cette fonctionnalité.

  ```bash
  npm install cors
  ```

  Exemple de configuration CORS dans Express :

  ```ts
  import cors from 'cors';

  app.use(cors());
  ```

- **Rate Limiting** : Pour éviter les attaques par déni de service (DoS), il est important de limiter le nombre de requêtes qu'un utilisateur peut effectuer sur une période donnée. Tu peux utiliser le middleware `express-rate-limit` pour cela.

  ```bash
  npm install express-rate-limit
  ```

  Exemple de configuration de rate limiting :

  ```ts
  import rateLimit from 'express-rate-limit';

  const limiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100, // Limite chaque IP à 100 requêtes par fenêtre
  });

  app.use(limiter);
  ```

:six: **Conseil** : Combine ces pratiques de sécurité avec HTTPS pour sécuriser les communications.

