---
layout: post
title: Frameworks coté Serveur
categories:
- WebDev2
- lecture
author: Yoann Pigné
published: true
update: 2024-09-09
---

On s'intéresse à la réalisation d'applications Web classiques (côté serveur) où les données dynamiques sont stockées dans des bases de données et les pages Web générées côté serveur à partir de templates. On parle aussi de **rendu côté serveur** (*server-side rendering*).

L'objectif est d'allier simplicité et modernité dans la conception d'applications web robustes et maintenables. Le framework **Express** avec un moteur de template permet de  générer des pages dynamiques côté serveur, tandis que **Mongoose** facilite la manipulation des données dans une base **MongoDB**. **Passport.js** est un outil populaire pour gérer l'authentification dans les applications Express. Enfin **TypeScript** apporte la vérification des types pour un code plus robuste.


## TypeScript

**TypeScript** est un sur-ensemble de JavaScript qui ajoute des fonctionnalités de typage statique au langage. Cela permet de détecter les erreurs de typage à la compilation plutôt qu'à l'exécution, ce qui rend le code plus robuste et plus facile à maintenir.


### Installation

On peut utiliser TypeScript avec Node.js en installant les outils suivants :

1. **TypeScript (`tsc`)** – Le compilateur TypeScript.
2. **ts-node** – Permet d'exécuter directement du TypeScript sans le compiler manuellement à chaque fois.

```bash
npm install -g typescript ts-node
```

#### Fichier de configuration TypeScript


Dans chaque projet TypeScript, on crée un fichier de configuration `tsconfig.json` pour spécifier les options de compilation.

```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true
  },
  "exclude": ["node_modules"]
}
```

#### Phases de Compilation et d'Exécution

On peut d'abord compiler puis exécuter le code TypeScript avec les commandes suivantes :

```bash
tsc
node dist/index.js
```
ou utliser ts-node pour compiler et exécuter en une seule commande :
```bash 
ts-node src/index.ts
```


#### Automatisation avec des scripts dans package.json

Pour simplifier l'exécution et la compilation on utulise des  **scripts** dans le fichier `package.json` :

```json
{
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "nodemon --exec ts-node src/index.ts",
    "deploy": "npm run build && npm start"
  }
}
```

On utilise **Nodemon** pour recharger automatiquement le serveur à chaque modification du code.

```bash
npm install --save-dev nodemon
```






## Web Application Frameworks

Les applications Web modernes, qu'elles soient côté client ou serveur, nécessitent des frameworks qui simplifient la gestion des requêtes HTTP, de la sécurité, de la mise en cache et de nombreuses autres fonctionnalités.

Les frameworks d'applications Web permettent de gérer la communication entre le client et le serveur, notamment :

- l'authentification,
- la sécurité/cryptographie,
- la compression,
- le routage,
- la journalisation,
- la gestion des sessions et cookies,
- l'équilibrage de charge,
- et bien d'autres fonctionnalités.

### Express avec TypeScript ([expressjs.com](https://expressjs.com/))

**Express** est un framework minimaliste pour Node.js, souvent utilisé pour la création rapide d'applications serveur. En combinaison avec TypeScript, on peut bénéficier de la vérification des types pour un code plus robuste.

#### Installation

```bash
npm install express 
npm install --save-dev @types/node @types/express
```

#### Exemple de serveur avec Express et TypeScript

Voici un exemple de serveur minimal avec TypeScript.

```typescript
import express, { Request, Response } from 'express';

const app = express();
const port = 1337;

app.get('*', (req: Request, res: Response) => {
  res.send('<h1>Hello, World!</h1>');
});

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}/`);
});
```

Pour exécuter ce code, créez un fichier `tsconfig.json` avec la configuration suivante :

```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true
  }
}
```

Compilez et exécutez avec :

```bash
tsc && node dist/index.js
```

### Routage avec Express

Dans une application Web, le routage détermine comment les URL sont associées à des fonctions spécifiques pour gérer les requêtes HTTP. Voici un exemple de routage avec TypeScript.

```typescript
app.get('/advert/:id?', (req: Request, res: Response) => {
    const id = req.params.id;
    res.send(`Vous avez demandé l'annonce avec l'id : ${id}`);
})
.get('/search', (req: Request, res: Response) => {
    const query = req.query.q;
    console.log('Requête de recherche : ' + query);
    res.send('Résultats de la recherche');
});
```

### Templates avec Express et EJS

Pour générer des pages dynamiques, **EJS** (Embedded JavaScript) est un bon moteur de template qui fonctionne très bien avec Express.

#### Installation

```bash
npm install ejs
```

#### Utilisation

Dans votre configuration principale (`app.ts`), configurez EJS comme moteur de rendu.

```typescript
app.set('view engine', 'ejs');
app.set('views', './views');
```

Voici un exemple de route qui utilise EJS pour rendre une vue.

```typescript
app.get('/user/:id', (req: Request, res: Response) => {
    const user = { id: req.params.id, name: 'Tom' };
    res.render('hello_user', { user });
});
```

Et dans le fichier `views/hello_user.ejs` :

```html
<div class="user">
    <h2>Hello, <%= user.name %>!</h2>
</div>
```

### Middleware Express

Les **middlewares** sont des fonctions qui interceptent les requêtes et réponses. Voici comment les configurer en TypeScript.

```typescript
import express, { Request, Response, NextFunction } from 'express';

const app = express();

// Middleware de journalisation
app.use((req: Request, res: Response, next: NextFunction) => {
  console.log(`${req.method} - ${req.url}`);
  next();
});

// Middleware pour gérer les erreurs
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  console.error(err.message);
  res.status(500).send('Erreur serveur');
});
```

## Bases de données

Les bases de données sont essentielles pour les applications Web. Avec TypeScript, les ORM modernes facilitent la manipulation des données tout en bénéficiant de la sécurité des types.

### MongoDB avec Mongoose

MongoDB est une base de données NoSQL orientée document qui stocke les données en JSON (BSON). **Mongoose** est un ODM (Object Data Modeler) populaire pour MongoDB, compatible avec TypeScript.

### Installation

```bash
npm install mongoose
```

### Exemple de connexion avec TypeScript

```typescript
import mongoose from 'mongoose';

const uri = 'mongodb://localhost:27017/test';

mongoose.connect(uri, {})
  .then(() => console.log('MongoDB connecté'))
  .catch(err => console.error('Erreur de connexion à MongoDB :', err));
```

### Définition d'un schéma avec TypeScript

```typescript
import mongoose, { Document, Model } from 'mongoose';

// Interface définissant le schéma Animal pour ajouter de la sécurité de type
interface IAnimal extends Document {
  name: string;
  type: string;
  age: number;
}

// Création du schéma Animal
const animalSchema = new mongoose.Schema<IAnimal>({
  name: { type: String, required: true },
  type: { type: String, required: true },
  age: { type: Number, default: 0 },
});

// Création du modèle Animal
const Animal: Model<IAnimal> = mongoose.model<IAnimal>('Animal', animalSchema);

// Création d'un nouvel objet
const dog = new Animal({ name: 'Paf', type: 'dog', age: 4 });
dog.save();
```

### Un exemple de requête


```typescript

(async () => {
  try {
    // Connexion à la base de données MongoDB
    await mongoose.connect('mongodb://localhost:27017/test', {
    });
    console.log('MongoDB Connected');

    // Création de plusieurs objets Animal
    const createDogs = [
      new Animal({ name: 'Paf', type: 'dog', age: 4 }),
      new Animal({ name: 'Tobi', type: 'dog', age: 5 }),
      new Animal({ name: 'BebePaf', type: 'dog' }),
    ];

    // Sauvegarde des animaux
    await Promise.all(
      createDogs.map(async (animal) => {
        try {
          await animal.save();
        } catch (err) {
          console.error('Error saving animal:', err);
        }
      })
    );

    // Recherche d'animaux dans la base de données
    let dogs = await Animal.find({ type: 'dog' })
      .where('age')
      .gt(2)
      .lt(8) // contrainte sur l'âge
      .sort({ age: -1 }) // tri par âge décroissant
      .select({ name: 1, age: 1 }) // sélection des colonnes
      .lean(); // conversion en objets JavaScript simples

    // Mise à jour de l'âge des chiens
    const updatedDogs = await Promise.all(
      dogs.map(async (dog) => {
        dog.age++;
        try {
          const updatedDog = await Animal.findByIdAndUpdate(dog._id, { age: dog.age }, { new: true });
          return updatedDog;
        } catch (err) {
          console.error('Error updating dog:', err);
          return null;
        }
      })
    );

    console.log('Updated dogs:', updatedDogs.filter(Boolean)); // Filtrage des null

  } catch (err) {
    console.error('Error connecting to MongoDB:', err);
  } finally {
    // Déconnexion de la base de données
    try {
      await mongoose.disconnect();
      console.log('MongoDB Disconnected');
    } catch (err) {
      console.error('Error disconnecting MongoDB:', err);
    }
  }
})();
```


### Utilisation de MongoDB avec Express


```js
app.post('/api/projects', auth.ensureAuthenticated, createProject);

function createProject(req, res) {
  var project = new Project(req.body);
  project.creator = req.user;
  project.save(function(err) {
    if (err) {
      res.status(500).json(err);
    } else {
      if (!req.user.hasProjects) {
        User.update({
          _id: req.user._id
        }, {
          hasProjects: true
        }, function(err) {
          if (err) {
            res.status(500).json(err);
          }
        });
      }
      res.json(project);
    }
  });
};
```


## Authentification avec Passport.js

Pour l'authentification, **Passport.js** est l'une des solutions les plus populaires pour les applications Express.

### Installation

```bash
npm install passport passport-local express-session @types/passport @types/passport-local @types/express-session
```

Voici un exemple de configuration simple de l'authentification locale avec Passport.js en TypeScript.

```typescript
import passport from 'passport';
import { Strategy as LocalStrategy } from 'passport-local';

passport.use(new LocalStrategy(
  (username, password, done) => {
    // Exemple de validation utilisateur
    if (username === 'admin' && password === 'secret') {
      return done(null, { id: 1, username: 'admin' });
    } else {
      return done(null, false, { message: 'Identifiants incorrects' });
    }
  }
));

app.use(require('express-session')({ secret: 'secret', resave: false, saveUninitialized: false }));
app.use(passport.initialize());
app.use(passport.session());
```

Pour plus d'exemples détaillés, consultez la documentation officielle de [Passport.js](http://www.passportjs.org/).