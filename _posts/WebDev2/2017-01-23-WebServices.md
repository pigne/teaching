---
layout: post
title: Web APIs / Web Services
categories:
- WebDev2
- lecture
author: Yoann Pigné
published: false
---


## Quelles technologie(s) utiliser ?

Plusieurs technologies pour faire des services web en fonction du type de service que l'on veut fournir au client.


### REST vs SOAP

Souvent 2 technologies s'oppose:

- REST
- SOAP

#### SOAP

SOAP est une technologie **pas seulement Web** (HTTP, SMTP, UDP, TCP, ...) dédiée à la mise en place d'outilse de manipulation de **services**. SOAP ne donne pas accès a des données, il donne accès à des services/actions dans une application serveur exposée.

SOAP utilise un langage XML normalisé (WSDL) pour décrire les services à utiliser et les actions à exécuter.

```java
switchProtocol(Protocol)
upgradeAccountToPremium(User)
resendAuthentificationToken(User, Token)
```

```xml
POST /InStock HTTP/1.1
Host: www.example.org
Content-Type: application/soap+xml; charset=utf-8
Content-Length: nnn

<?xml version="1.0"?>

<soap:Envelope
xmlns:soap="http://www.w3.org/2003/05/soap-envelope/"
soap:encodingStyle="http://www.w3.org/2003/05/soap-encoding">

<soap:Body xmlns:m="http://www.example.org/stock">
  <m:GetStockPrice>
    <m:StockName>IBM</m:StockName>
  </m:GetStockPrice>
</soap:Body>

</soap:Envelope>
```


L'extensibilité offre de nouvelles fonctionnalités (sécurité, atomicité, etc..)

#### REST

REST est une technologie **Web** dédiée à la mise à disposition d'une interface publique (API) pour l'accès et la manipulation (CRUD) de **données**.

REST ne fonctionne qu'avec HTTP(s) et utilise ses mécanismes (URI) mais ne limite pas les formats de données.

```java
getUserInformation(UserId)
addProductToBasket(ProductId, BasketId)
```

Les requêtes REST sont sans état (*stateless*). On peut décrire une API REST avec les mécanismes de HTTP (méthodes, URI, codes de retour).

Par exemple on peut utiliser un outil comme [Swagger](http://swagger.io/) pour :
- définir des API REST
- auto-générer les codes de serveurs et de clients pour ces API.



#### Pros / Cons

- A priori SOAP est plus complet et général que REST, surtout du fait de son extensibilité.
- SOAP donne beaucoup de pouvoir au client (concevoir des requêtes complexes avec jointures, filtres, etc.).
- REST ne permet qu'un accès simple aux données. Une requête complexe (avec jointures, filtres, etc.) nécessitera plusieurs appel REST.
- REST est sans état (*stateless*) et permet la mise en place de mécanismes de **cache**. SOAP n'est pas *stateless*, donc **pas de cache possible**.
- il semble que REST est plus facile a mettre en place et à maintenir que SOAP.

### d'autres modèles existent

*Facebook* propose [*GraphQL*](http://graphql.org/), une approche proche de REST avec les avantages (requêtes complexes) de SOAP. *GraphQL* est un langage de description qui permet au client de décrire le type de réponse qu'il désire recevoir du server.

- Dédié à la manipulation de données uniquement (comme REST)
- Requête dans un langage dédié (*GraphQL*) (à l'image des requêtes WSDL de SOAP) pour décrire la requête.
- Réponses sous forme d'objets JSON.


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
   "title": "My new house in the city",
   "text": "<h2>Great new house</h2>",
   "plot_id": "-628141",
   "available_date": "2012-12-14",
   "high_priority": false,
   "surface_area": 230
}
```

- Each document is identified by an `_id` field of default type `ObjectId`.
- Administration is done via the mongo shell (JS)


### Mongoose ([mongoosejs.com](http://mongoosejs.com/)) an ODM (Object Document Mapper) for MongoDB and Node.


Create new Model Objects.

```javascript
var mongoose = require('mongoose');
var Schema = mongoose.Schema;
var animalSchema = new Schema(
  {
    name: String,
    type: String,
    age: { type: Number, default: 0 },
    birthday: { type: Date, default: Date.now },
  }
);
var Animal = mongoose.model('Animal', animalSchema);
var dog = new Animal({ type: 'dog' });
```

Connect to a Mongo database and query these objects.

```javascript
var mongoose = require('mongoose');
mongoose.connect("some mongodb url...");

var Animal  = mongoose.models.Animal,
var dogs;

Animal.
  find({ type: 'dog' }).
  where('age').gt(2).lt(8).
  sort({ age: -1 }).
  select({ name: 1, age: 1 }).
  exec(callback);

function callback(data){
    dogs = data;
    console.log(dogs);
    let thisDog = dogs[0];
    thisDog.age.$inc();
    thisDog.save();

}
```


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



## Express ([expressjs.com](http://expressjs.com/)). A minimalist Web framework for node.


```javascript
var express = require('express');

var app = express();

app.get('*', function(req, res){
  res.send('<h1>Hello World</h1>');
});
var server = app.listen(1337);
```

Install with npm: `npm install express --save`


### Routing with Express

In a Web App each resource is accessed through one unique request (URI)
Requests are actions on elements or collections (get, create, modify, delete)
Close to the idea of RESTfull applications (Representational State Transfer)
Express link URIs to actions through HTTP verbs (GET, PUT, POST, DELETE)

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
- Templating in node with Jade: http://jade-lang.com/

In the main express configuration file (`app.js`):

```javascript
app.set('view engine', 'jade');
var users = [{id:1, name:'Tom'},
            {id:2, name:'Max'}];
app.get('/user/:id?', function(req, res){
  res.render('hello_user', _.filter(users, {id:req.param('id')[0]);
});

```
In a template file (`/views/hello_user.jade`):
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
var express = require('express');
var path = require('path');
var favicon = require('serve-favicon');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');

var routes = require('./routes/index');

var app = express();

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'jade');

app.use(logger('dev'));
app.use(favicon(__dirname + '/public/img/favicon.ico'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({
  extended: true
}));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/', routes);

/// catch 404 and forward to error handler
app.use(function(req, res, next) {
    var err = new Error('Not Found');
    err.status = 404;
    next(err);
});

/// error handler
app.use(function(err, req, res, next) {
    res.status(err.status || 500);
    res.render('error', {
        message: err.message,
        error: {},
        title: 'error'
    });
});

```


Usage with Mongodb.

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
