---
layout: post
title: Frameworks coté Serveur
categories:
- WebDev2
- lecture
author: Yoann Pigné
published: true
---


On s'intéresse à la réalisation d'application Web classiques (coté serveur) ou les données dynamiques sont stockées dans les bases de données et les pages Web générées coté serveur à partir de templates.

## Web Application Frameworks

WebApps (client-side as well as server-side) need web servers and other network related frameworks.

Web Application frameworks handle all the technical parts of the communication between client and server

- authentication
- security / cryptography
- compression
- routing
- logging
- quality of service
- sessions
- cookies
- load balancing
- ...



### Express ([expressjs.com](https://expressjs.com/)). A minimalist Web framework for node.


```javascript
let express = require('express');

let app = express();

app.get('*', function(req, res){
  res.send('<h1>Hello World</h1>');
});
let server = app.listen(1337);
```

Install with npm: `npm install express --save`

Use `express-generator` in order to create a scaffold project: 

```sh
npm i -g express-generator
npx express-generator --view=pug nomProjet
```

### Routing with Express

In a Web App each resource is accessed through one unique request (URI)
Requests are actions on elements or collections (get, create, modify, delete)
Close to the idea of RESTfull applications (Representational State Transfer)
Express links URIs to actions through HTTP verbs (GET, PUT, POST, DELETE)

```javascript
app.get('/advert/:id?', function(req, res) {
    res.send('You asked for advert' + req.param('id'));
})
.get('/search', function(req, res) { //search?q=something+fun
    console.log('the search query is: ' + req.query.q); // req.param('q')
})
.post('/advert', function(req, res){ // with the bodyParser() middleware
    req.body.advertTittle
})
.delete('/advert/:id?', function(req, res) {
    // remove entry req.param('id') from database
});
```

### Templates with Express

- Templates allow the dynamic creation of web pages.
- Templates use a special language with variables (user data) loops and conditions
- Templates are stored in the views/ folder of the App and are called from a route.

In the main express configuration file (`app.js`):

```javascript
app.set('view engine', 'pug');
var users = [{id:1, name:'Tom'},
            {id:2, name:'Max'}];
app.get('/user/:id?', function(req, res){
  res.render('hello_user', users.filter( user => user.id === req.param('id') )[0]);
});
```

In a template file (`views/hello_user.pug`) using the *Pug* (<https://pugjs.org>) templating library:

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

### Express's Middlewares

- Extra features given to the application. They are executed at each request.
- Executed sequentially. The order **is** important.
- They use 4 parameters:
  - `err`: the error messages
  - `req`: the user request object
  - `res`: the response object to be sent back
  - `next`: a callback to the next middleware to call
- Middlewares are configured with the use() function.
- logger, csrf, compression, authentication, bodyParser(forms), json, cookies, sessions, static, ...
- middleware modules are installed on a per-module basis (`npm install body-parser`)

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

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'pug');

app.use(logger('dev'));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/', indexRouter);
app.use('/users', usersRouter);

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  next(createError(404));
});

// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});
```

## Databases

Of course classical relational DBMS hold for web apps, but NoSQL type DBMS become useful in case of:

- loosely structured (few or no foreign keys),
- no need to JOIN tables,
- Big Data.

4 types of noSQL DBMs:

- key-value stores
- object-based
- table-based
- graph-based

Famous projects:

- Project Voldemort, used by LinkedIn
- Cassandra Project, by Apache, formally used by Facebook
- Dynamo, by Amazon
- HBase, by Apache Hadoop, used by Facebook
- CouchDB (JSON store), by Apache
- MongoDB (JSON/BSON store)
- BigTable, by Google
- Neo4J (Graph Database)

### MongoDB([www.mongodb.org](http://www.mongodb.org/)): an object-oriented storage noSQL Database

Documents are stores in a JSON-like format (BSON: binary representation of JSON).

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

- Each document is identified by an `_id` field of default type `ObjectId`.
- Administration is done via the mongo shell (JS)

### Mongoose ([mongoosejs.com](http://mongoosejs.com/)) an ODM (Object Document Mapper) for MongoDB and Node.

Create new Model Objects.

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

Connect to a Mongo database and query objects.

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
    .select({ name: 1, age: 1 }); // selectin de colonnes
  //.lean() // conversion en objets JS simples

  const saveDogs = dogs.map(async (dog) => {
    dog.age++;
    const saveDog = await dog.save().catch((err) => console.log("SAVE error"));
    return saveDog;
  });

  dogs = await Promise.all(saveDogs);

  console.log(dogs);

  mongoose.disconnect().catch((err) => console.log("DISC", err));
})(); // async IIFE
```

Mongodb usage with Express.

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

## Moteur de template

Pour l'heure, préférer un autre moteur de template que celui par défaut (pug) car il a des dépendances avec vulnérabilités. `Blade` est compatible avec la syntaxe de jade/pug.

## Authentification

Plusieurs solutions sont possibles, mais [Passport](http://www.passportjs.org/)) semble la plus viable. Ce [billet de blog](https://mherman.org/blog/local-authentication-with-passport-and-express-4/) donne un bon exemple. 