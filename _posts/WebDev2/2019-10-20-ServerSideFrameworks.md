---
layout: post
title: Frameworks coté Serveur
categories:
- WebDev2
- lecture
author: Yoann Pigné
published: true
update: 2023-09-11
---


On s'intéresse à la réalisation d'applications Web classiques (côté serveur) où les données dynamiques sont stockées dans des bases de données et les pages Web générées côté serveur à partir de templates.


## Web Application Frameworks

Les applications Web (côté client ainsi que côté serveur) ont besoin de serveurs Web et d'autres frameworks liés au réseau.

Les frameworks d'applications Web gèrent toutes les parties techniques de la communication entre le client et le serveur, notamment :

- l'authentification, 
- la sécurité/cryptographie, 
- la compression, 
- le routage, 
- la journalisation, 
- la qualité de service, 
- les sessions, 
- les cookies, 
- l'équilibrage de charge, 
- et bien d'autres encore.



### Express ([expressjs.com](https://expressjs.com/)). Un framework Web minimaliste pour Node.js.


Les frameworks d'applications Web sont des outils essentiels pour simplifier le développement d'applications Web. Ils gèrent divers aspects techniques tels que la gestion des requêtes HTTP, la sécurité, le routage, et plus encore. Express est l'un de ces frameworks, spécialement conçu pour Node.js


```javascript
const express = require('express');
const port = 1337;

const app = express();

app.get('*', function(req, res){
  res.send('<h1>Hello World</h1>');
});
let server = app.listen(port, ()=>console.log(`http://localhost:${port}/`));
```

Installez-le avec npm : npm install express

Utilisez express-generator pour créer un projet de base :

```sh
npm i -g express-generator
npx express-generator --view=pug nomProjet
```


### Routage avec Express

Dans une application Web, le routage fait référence à la manière dont les URL (Uniform Resource Locators) sont associées à des actions spécifiques. Express facilite la création de routes en associant des URL à des fonctions de traitement pour gérer les différentes actions HTTP telles que la récupération (GET), la création (POST) et la suppression (DELETE) de ressources. Les paramètres de route, comme :id?, permettent de capturer des valeurs variables dans l'URL.


```javascript
app.get('/advert/:id?', function(req, res) {
    res.send('You asked for advert' + req.params.id);
})
.get('/search', function(req, res) { //search?q=something+fun
    console.log('the search query is: ' + req.query.q); // req.param('q')
})
.post('/advert', function(req, res){ // avec le middleware bodyParser() 
    req.body.advertTittle
})
.delete('/advert/:id?', function(req, res) {
    // supprimer l'entrée req.param('id') de la base de données
});
```

### *Templates* avec Express

- Les *templates* (modèles de pages) permettent la création dynamique de pages Web.
- Les *templates* utilisent un langage spécial avec des variables (données utilisateur), des boucles et des conditions.
- Les *templates* sont stockés dans le dossier `views/` de l'application et sont appelés depuis une route.

Dans le fichier de configuration principal d'Express (`app.js``):

```javascript
app.set('view engine', 'pug');
var users = [{id:'1', name:'Tom'},
            {id:'2', name:'Max'}];
app.get('/user/:id?', function(req, res){
  res.render('hello_user', users.filter( user => user.id === req.params.id )[0]);
});
```

Dans un fichier *template* (`views/hello_user.pug`) en utilisant la bibliothèque *Pug* (<https://pugjs.org>):


```
.user
  h2 Hello #{name}!
```

Result:

```xml
<div class="user">
    <h2>Hello Max!</h2>
</div>
```

### Middleware Express

- Les middlewares sont des fonctionnalités supplémentaires fournies à l'application. Ils sont exécutés à chaque requête.
- Ils sont exécutés séquentiellement, l'ordre est important.
- Ils utilisent quatre paramètres :
  - `err` : les messages d'erreur
  - `req` : l'objet de requête utilisateur
  - `res` : l'objet de réponse à renvoyer
  - `next` : un rappel pour appeler le middleware suivant
- Les middlewares sont configurés avec la fonction `use()`.
- Exemples de middlewares : journalisation, protection CSRF, compression, authentification, bodyParser (formulaires), json, cookies, sessions, static, etc.
- Les modules de middleware sont installés module par module à l'aide de `npm install`.

### Example classical Express middleware configuration

```javascript
var createError = require('http-errors');
var express = require('express');
var path = require('path');
var cookieParser = require('cookie-parser');
var logger = require('morgan');

var indexRouter = require('./routes/index');
var usersRouter = require('./routes/users');

var app = express();

// Configuration du moteur de vues
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'pug');

app.use(logger('dev'));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/', indexRouter);
app.use('/users', usersRouter);

// Gestion des erreurs 404 et renvoi vers le gestionnaire d'erreurs
app.use(function(req, res, next) {
  next(createError(404));
});

// Gestionnaire d'erreurs
app.use(function(err, req, res, next) {
  // Configuration des variables locales, fourniture de l'erreur uniquement en mode développement
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // Renvoi de la page d'erreur avec le code d'erreur approprié
  res.status(err.status || 500);
  res.render('error');
});
```

## Bases de données

ien sûr, les systèmes de gestion de base de données relationnelles classiques sont adaptés aux applications Web, mais les systèmes de base de données NoSQL peuvent être utiles dans les cas suivants :

- Données peu structurées (peu ou pas de clés étrangères).
- Pas besoin de tables de jointure.
- Big Data.

Il existe (au moins) quatre types de systèmes de base de données NoSQL :

- key-value
- object-based
- table-based
- graph-based

Projets célèbres:

- Apache Cassandra : projet open source de base de données NoSQL distribuée. Initialement développée par Facebook.
- Elasticsearch : Elasticsearch est une base de données de recherche et d'analyse distribuée, souvent utilisée pour la recherche en texte intégral et l'analyse de données.
- DynamoDB, par Amazon Web Services, pour les flux de données
- CouchDB : CouchDB est une base de données NoSQL orientée document qui stocke les données au format JSON. Il est conçu pour la facilité d'utilisation et la réplication.
- MongoDB : base de données NoSQL de stockage orientée objet qui utilise BSON pour le stockage des données.
- Redis : Bien que Redis soit souvent classé comme une base de données de type clé-valeur, il est également utilisé pour le stockage en mémoire rapide et la mise en cache.
- Neo4j : base de données NoSQL de type graph, idéale pour la gestion de données orientées graphs (réseaux), comme les réseaux sociaux et les recommandations.

### MongoDB([www.mongodb.org](http://www.mongodb.org/)): une base de données NoSQL de stockage orientée objet

Les documents sont stockés dans un format JSON-like (BSON : représentation binaire de JSON).

```javascript
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),
  "title": "My new house in the city",
  "text": "<h2>Great new house</h2>",
  "plot_id": "-628141",
  "available_date": ISODate("2018-11-17T08:30:00.000Z"),
  "high_priority": false,
  "surface_area": 230
}
```

- Chaque document est identifié par un champ `_id` de type `ObjectId` par défaut.
- L'administration se fait via la console mongo (JavaScript).

### Mongoose ([mongoosejs.com](http://mongoosejs.com/)) un ODM (Object Document Mapper) pour MongoDB et Node.

Créer de nouveaux objets modèle.

```javascript
const mongoose = require('mongoose');
const Schema = mongoose.Schema;
const animalSchema = new Schema(
  {
    name: String,
    type: String,
    age: { type: Number, default: 0 },
    birthday: { type: Date, default: Date.now },
  }
);
const Animal = mongoose.model('Animal', animalSchema);
const dog = new Animal({ type: 'dog' });
```

Connexion à une base de données Mongo et requête d'objets.

```javascript
(async () => {
  await mongoose
    .connect("mongodb://localhost:27017/test", {
      useNewUrlParser: true,
      useUnifiedTopology: true,
    })
    .catch((err) => console.log("CONNECT", err));

  const Animal = mongoose.models.Animal;

  const createDogs = [
    new Animal({ name: "Paf", type: "dog", age: 4 }),
    new Animal({ name: "Tobi", type: "dog", age: 5 }),
    new Animal({ name: "BebePaf", type: "dog" }),
  ].map(async (animal) => {
    await animal.save().catch((err) => console.log("save error", err));
  });
  await Promise.all(createDogs);

  let dogs = await Animal.find({ type: "dog" })
    .where("age")
    .gt(2)
    .lt(8) // contrainte
    .sort({ age: -1 }) // tri
    .select({ name: 1, age: 1 }); // selection de colonnes
  //.lean() // conversion en objets JS simples

  const saveDogs = dogs.map(async (dog) => {
    dog.age++;
    const saveDog = await dog.save().catch((err) => console.log("SAVE error"));
    return saveDog;
  });

  dogs = await Promise.all(saveDogs);

  console.log(dogs);

  mongoose.disconnect().catch((err) => console.log("DISCONNECT", err));
})(); // async IIFE
```

Utilisation de MongoDB avec Express.


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

<!-- ## Moteur de template

Pour l'heure, préférer un autre moteur de template que celui par défaut (pug) car il a des dépendances avec vulnérabilités. `Blade` est compatible avec la syntaxe de jade/pug. -->

## Authentification

Plusieurs solutions sont possibles, mais [Passport](http://www.passportjs.org/)) semble la plus viable. Ce [billet de blog](https://mherman.org/blog/local-authentication-with-passport-and-express-4/) donne un bon exemple. 