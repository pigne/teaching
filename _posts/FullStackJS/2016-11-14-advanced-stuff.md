---
layout: post
title: Advanced Web Technologies and Protocols
categories:
- FullStackJS
- lecture
author: Yoann Pigné
published: true
---


This document presents some advanced technics and protocols related to Web technologies. Some are new standards that are not fully supported by all browsers.

##  *TypedArray* & *ArrayBuffer*

Les [*TypedArray*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) sont des représentation **typées** de tableaux de valeurs. La structure de donnée sous-jacente est le [*ArrayBuffer*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) (un buffer binaire). Leur accès est est beaucoup plus rapide que les tableaux classiques. Plusieurs types sont disponibles :

```js
Int8Array();
Uint8Array();
Uint8ClampedArray();
Int16Array();
Uint16Array();
Int32Array();
Uint32Array();
Float32Array();
Float64Array();
```


## WebWorker

A simple mechanism that permits the creation of threads that run scripts. Also provides communication facilities :

- a messaging mechanisms to communicate between the WebWorker and the main page
- APIs to communicate with the server, access data stores, use the graphic card, read files, etc.

WebWorkers have a different context than the main app so **no shared memory**. However, Transferrable Objects can be transferred from one thread to the other.

### Demonstration of a WebWorker-Enabled App

The [WebWorker](https://github.com/ULH-WebDevelopment/WebWorkerDemo) Web App is a simple demonstration of the usage of WebWorkers. The app shows that heavy computation work can be done on WebWorkers without affecting the main process' performances.

### The main App

The first part of the app shows a rotating rainbow-colored disc rendered at optimal frame rate (60 fps) using the  [`requestAnimationFrame()`](https://developer.mozilla.org/fr/docs/Web/API/Window/requestAnimationFrame) function. The code for this animation is located in the `public/anim.js` file.

The lower part is composed of a rectangular area (1000x300) filled with randomly colored pixels. This area is composed of 10 arrays of random data coding for the color of each pixel. Each array of data is given to a WebWorker that does some computation on it and then return it back to main process.

#### Creating workers

The app creates 10 WebWorkers and assign them a script name (`ws.js`) that they will load :

```javascript
// Creation of 10 WebWorkers
var workers = [];
for (var i = 0; i < 10; i++) {
  workers.push(new Worker("ww.js"));
}
```

#### sending data to workers

An array of data is created foreach worker. We use [TypedArray](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) and binary buffers ([ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)). These are fast _typed_ arrays.

```javascript
var SIZE = 100 * 300 * 4;
arrayBuffer = new ArrayBuffer(SIZE);
view = new Uint8Array(arrayBuffer);
// Initialize with random values within [0,255]
// ...
```

Sending the data with an unique id to the worker so it can start working. The last parameter (`[view.buffer]`) is the reference of the array included in the `data` attribute that will be transferred to the worker.

```javascript
var data = {
  'id': workers_id++,
  'data': view.buffer
};
worker.postMessage(data, [view.buffer]);
```


#### Receiving Data

Data can be received from the worker thanks to a message passing mechanism with event listeners.  

When message is received from a WebWorker, the main app immediately renders it on the canvas. We do not wait for the _animation frame event_ or we could loose the access to the data.

```javascript
worker.addEventListener("message", handler, false);
function handler (event){
  // get index and data from this worker
  var id = event.data.id;
  var data = event.data.data;
  // create a image from the binary data
  var arr = new Uint8ClampedArray(data);
  // draw the image on the dedicated canvas (shift by the `id` value)
  ctx.putImageData(new ImageData(arr, 100, 300), id * 100, 0);
}
```



### The WebWorker

The aim of the WebWorker, although not really efficient or realistic, is to somehow search for the average color from the data set that is given.

The worker  mimics the behavior  of a genetic algorithm trying to optimize the colors in the dataset. This data is represented as a 2D array. One line is seen as a solution vector (a chromosome). The set off all lines are the population of chromosomes. Given a fitness function, classical genetic algorithms (GAs) iteratively try to modify the dataset in order to improve the fitness. The GA selects 2 random chromosomes (2lines of the dataset), then create a third one that is a combination of both (the offspring chromozome). If this new chromosome has a better fitness than one of the parents it takes that parent's place in the dataset.

The fitness here is the pairwise difference between any pair of adjacent colors of the vector. The lower the difference, the better the fitness.  

When started the worker waits for messages containing the data to be used for the algorithm.

After the computation, it sends a message back to the main app transferring the data again.

```javascript
postMessage({
  'id': id,
  'data': arr.buffer,
  'fitness': maxFitness,
  'round':round++,
  'index':maxIndex
}, [arr.buffer]);
```

### Analysis


Although the algorithm needs a lot of CPU cycles the most extensive task is the exchange of information. Although this example uses transferable objects, data transfer is always a bottleneck.




## WebSocket

WebSocket is an evolved communication protocol that allow full duplex communication between the server and the client. It uses an event based model so that server and client can be notified went messages arrive. The client does not have to ask the server for available data, the server can send it directly.

It relies on the underlying TCP connection opened by HTTP but does not use HTTP. It can support text of binary data.

### A WebSocket Demo

<https://www-apps.univ-lehavre.fr/forge/WEB-IHM/web-socket-demo.git>

This project is a simple WebSocket demo. It focusses on showing the basic mechanisms used to create a bidirectional (full duplex) communication WebSocket.

The application is a simple group chat, where any connected client receives messages sent by everyone. There is no [Same-origin policy](https://en.wikipedia.org/wiki/Same-origin_policy) control and security involved.

The application code is two-folded: the server code and the client code.


## The server

In development mode two server are used. One for the static contents of the Web app (html, js, css) and one for the WeBSocket management. The Websocket server uses the basic `http` project plus the  [ws](https://einaros.github.io/ws/) WebSocket implementation.

The WebSocket server is created atop an HTTP server on a different port than the static assets  (html, css, js files) server from Webpack. The Webpack dev server is configured to proxy the WebSocket requests to the WS server.  

What the WS server does is :

- Create an HTTP server
- Create a WebSocket Server and bind it to the HTTP server
- The WebSocket server will maintain a list of connected  WebSocket clients
- Greet any new connection
- Wait for messages from any client and broadcast the message to all the connected clients.

What the static assets server does is :

- serve static assets (html, css, images, js files)
- proxy the WS requests to the WS server


## The client

The client uses the default implementation of the WebSocket standard that is implemented on the browser (good overall support).

What the client does:

- Retrieve a client name from the cookies
- If the name does not exist, then create a new random one.
- Create a WebSocket connection to the server and wait for messages
- On receiving a message, an `<li>` element is created with the content of the message and added to the page
- When text is entered in a form input on the page, and the return key is stroke, the text of the input field is send through the WebSocket, to the server.


## Analysis

This application is a very basic group chat with very little control and no security. The aim here is to show that a very few number of lines of code can already provide great communication facilities.

A more robust application would use a third party library that would take care of security, protocol mismatches and browser support. [Socket.io](http:socket.io) is one of the most achieved projects for WebSockets.

## Promise

Les [*Promise*](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/Promise) (promesse) sont des objets permettant d'effectuer des traitements asynchrones. Une promesse représente le résultat renvoyé par l'exécution d'une fonction asynchrone. Ce résultat peut être disponible immédiatement après l'exécution, plus tard, voir jamais.

```js
new Promise( /* exécuteur */ function(resolve, reject) { ... } );
```

La fonction *exécuteur* prends 2 paramètres : `resolve` et `reject`. Ce sont des fonctions. l'exécuteur commence le travail asynchrone. Si le travail s'exécute sans problème la fonction `resolve`est exécutée, s'il y a une erreur, `reject` est exécuté.


```js
function trucQuiprendDuTemps() {
  return new Promise((resolve, reject) =>
  {
    setTimeout(
      function() {
      var r = Math.random();
      	if(r > 0.1) {
  	      resolve(r);
        } else {
        	reject(r);
        }
      }, 1000
    );
  })
}

const p = trucQuiprendDuTemps();

p.then(
  (r) => {console.log("Yes!", r)},
  (r) => {console.log("Erreur ! Pas de chance", r)}
)
```



## Fetch

Fetch est une novelle fonctionnalité ([*Living Standard*](https://fetch.spec.whatwg.org/)) qui unifie et simplifie la manière de transférer de l'information entre un navigateur et un serveur. Son but est de remplacer le standard  `XMLHttpRequest`. Elle propose une définition générique des objets `Request` et `Response` et repose sur l'utilisation des `Promise`.

```js
fetch(url, options).then((response) => {
  // handle HTTP response
}, (error) => {
  // handle network error
})
```

- `url` est relatif ou absolue. Si l'url pointe sur un autre nom de domaine, les rêgles du CORS (*cross-origin HTTP request*) s'appliquent. On peut donc faire des requêtes *fetch* sur un autre serveur que celui d'origine.
- `options` :
  - `method` (String) - HTTP request method. Default: "GET"
  - body (String, [Blob](https://developer.mozilla.org/fr/docs/Web/API/Blob), [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData/Using_FormData_Objects)) - HTTP request body
  - headers (Object, [Headers](https://developer.mozilla.org/fr/docs/Web/API/Headers)) - Default: `{}`
  - credentials (String) - Authentication credentials mode. Default: "omit"
    - "omit" - don't include authentication credentials (e.g. cookies) in the request
    - "same-origin" - include credentials in requests to the same site
    - "include" - include credentials in requests to all sites


### Différences avec `jQuerry.ajax()`

- La *promise* retournée par *fetch* n'échoue qu'en cas de problème réseau. Un code de retout 4xx ou 5xx dans le status de la réponse  n'est pas une erreur réseau.
- par défaut, *fetch* n'envoie pas de cookies (`credentials : "omit"`)

### examples from (https://github.com/github/fetch) :


#### Status différent de 2xx

```js
fetch(...).then(function(response) {
  if (response.ok) {
    return response
  } else {
    var error = new Error(response.statusText)
    error.response = response
    throw error
  }
})
```


#### HTML

```javascript
fetch('/users.html')
  .then(function(response) {
    return response.text()
  })
  .then(function(body) {
    document.body.innerHTML = body
  })
```

#### JSON

```javascript
fetch('/users.json')
  .then(function(response) {
    return response.json()
  }).then(function(json) {
    console.log('parsed json', json)
  }).catch(function(ex) {
    console.log('parsing failed', ex)
  })
```

#### Response metadata

```javascript
fetch('/users.json').then(function(response) {
  console.log(response.headers.get('Content-Type'))
  console.log(response.headers.get('Date'))
  console.log(response.status)
  console.log(response.statusText)
})
```

#### Post form

```javascript
var form = document.querySelector('form')

fetch('/users', {
  method: 'POST',
  body: new FormData(form)
})
```

#### Post JSON

```javascript
fetch('/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    name: 'Hubot',
    login: 'hubot',
  })
})
```

#### File upload

```javascript
var input = document.querySelector('input[type="file"]')

var data = new FormData()
data.append('file', input.files[0])
data.append('user', 'hubot')

fetch('/avatars', {
  method: 'POST',
  body: data
})
```



## ServiceWorker (comming soon)

<!-- TODO -->

## WebComponents (cooming soon)

<!-- TODO -->

## Generator (cooming soon)

<!-- TODO -->
