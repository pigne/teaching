---
layout: post
title: Dynamic Graphical Data
categories:
- WebDev2
- lecture
author: Yoann Pigné
published: true
update: 2021-11-07
---



- [2D Drawing on the _canvas_](#2d-drawing-on-the-canvas)
- [3D Drawing with _WebGL_](#3d-drawing-with-webgl)
- [SVG](#svg)
- [D3.js <small>Data-Driven Documents</small>](#d3js-smalldata-driven-documentssmall)


One of the main purposes of the Web is to present and exchange information. Information presentation can have many forms (text, graphics, audio, video, maps).

Graphically displaying information can be done in a static way or in a dynamic way.

- Static graphics are basically images (pictures) or pre-generated charts.
- Dynamic graphics occur when data, in a non graphical format (structured text, binary data), are transformed into  graphics (charts).

These graphics can be generated on the server (e.g. a PHP script that generates a charts based on the results of a MySQL DB query). Or they can be generated on the client side with JavaScript, HTML, and CSS .  

HTML5 has mainly 2 types technologies to display graphical data:

  - A pixel-based model for drawing:
      - in 2D with _canvas_
      - in 3D with _WebGL_
  - A DOM-based model that gives 2D elements DOM capabilities (events, positioning, styling, etc.): _SVG_

## 2D Drawing on the _canvas_

Canvas is primarily a [DOM element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas)
 that gives access to a 2D area for drawing.


A native API is available in JS:  `CanvasRenderingContext2D`.

```javascript
// <canvas id="my-canvas" width="200" height="200"></canvas>
var canvas = document.getElementById('my-canvas');
var context = canvas.getContext('2d');
context.fillStyle = "rgb(200,0,0)";
context.fillRect (10, 10, 100, 100);
```



Basic drawing capabilities :

- various shapes (rectangles, lines, paths, arcs)
- text
- Fill and stroke styles
- gradients & shadows
- transformations (scale, translate, rotate)
- images manipulation (at pixel level)

[A basic example on CodePen](http://codepen.io/pigne/pen/BoMwKz?editors=001)


More advanced 2D drawing with animations, events, user interaction can be achieved with more coding. Usually Extra libraries are used:

- general purpose 2D:
  - <http://libcanvas.github.io/>
  - <http://processingjs.org/>

- 2D game engines:
  - <https://playcanvas.com/>
  - <http://www.pixijs.com/>


## 3D Drawing with _WebGL_

WebGL is an API based on OpenGL ES. It uses the `canvas` DOM element to build 3D scenes.

Since OpenGL ES  is a low level API, we mostly always use third party libraries:

- <http://threejs.org/>
- <http://www.pixijs.com/> for 2D with WebGL

## SVG

SVG is an XML markup language for describing two-dimensional vector graphics.

```html
<svg width="100" height="50" viewBox="0 0 3 2">
    <rect width="1" height="2" x="0" fill="#002395" />
    <rect width="1" height="2" x="1" fill="#ffffff" />
    <rect width="1" height="2" x="2" fill="#ED2939" />
</svg>
```

<svg width="100" height="50" viewBox="0 0 3 2">
    <rect width="1" height="2" x="0" fill="#002395" />
    <rect width="1" height="2" x="1" fill="#ffffff" />
    <rect width="1" height="2" x="2" fill="#ED2939" />
</svg>

### SVG Elements

#### Shape elements

```html
<circle>, <ellipse>, <line>, <path>, <polygon>, <polyline>, <rect>
```

#### Structural elements

```html
<defs>, <g>, <svg>, <symbol>, <use>
```

#### Text content elements

```html
<altGlyph>, <textPath>, <text>, <tref>, <tspan>
```

### SVG Attributes

Mostly for Animations and Events.

Writing SVG is not trivial. Once again we use libraries. May exist. The most achieved (and maybe the most difficult to learn) is D3.js

## [D3.js](http://d3js.org/) <small>Data-Driven Documents</small>

The general purpose of [D3.js](http://d3js.org/) is to bind arbitrary data to a Document Object Model (DOM).

[D3.js](http://d3js.org/) allow to create a graphical representation of a data model (e.g. an array of numbers becomes a pie chart, a set of coordinates becomes a map, etc.)

### The Design Pattern

Classical steps to **create** data-linked DOM elements:

1. Start from one existing DOM element
2. Make a selection (<i>empty</i>) of DOM elements
3. Link data to the selection
4. Append new DOM elements to the selection (one Element per data item)
5. modify the new DOM elements :
   1. style
   2. attributes
   3. events

```html
<svg width=200 height=200>
```

```javascript
var user_data = [10, 20, 35, 12];
var svg = d3.select('svg'); // (1)
console.log(svg);
svg.selectAll('.block') // (2)
  .data(user_data) // (3)
  .enter() // data to be linked to DOM
  .append("circle") // (4)
  .classed("block", 1) // (5.1)
  .attr("cx", function(d, i) { // (5.2)
    return 100 * (1 + i);
  })
  .attr("cy", 100)
  .attr("r", function(d) {
    return d;
  });

```

[this example on CodePen](http://codepen.io/pigne/pen/epxMMY)

Mais on peut aussi **mettre à jour** et **supprimer** des éléments pour les synchroniser avec les données.

Idéalement on doit prévoir les 3 étapes : création, mise à jour et suppression.

```js
var text = g
  .selectAll("text")
  .data(data); // JOIN

text.exit() // EXIT
    .remove();

text.enter() // ENTER
  .append("text")
    .text(function(d) { return d; })
  .merge(text) // ENTER + UPDATE
    .attr("x", function(d, i) { return i * 32; });
```


Lire la suite sur le site de d3 :

- [Une introduction](https://d3js.org/#introduction).
- [La notion de jointure](https://bost.ocks.org/mike/join/)
- [La fonction `join()`](https://observablehq.com/@d3/selection-join)

### Drawing paths

```javascript
var p1 = 'M 0 0 L 0 10 L 10 10 L 20 10 L 20 20 L 30 20 L 30 30 L 30 40 ';
var p2 = 'M 10 20 L 20 20 L 20 30 L 10 30 L 10 20';

var paths = [{
  path: p1,
  fill: false
}, {
  path: p2,
  fill: true
}];

var svg = d3.select('svg');
console.log(svg);
svg.append("g")
  .attr("transform", "scale(10)")
  .selectAll('.block') // (2)
  .data(paths) // (3)
  .enter() // data to be linked to DOM
  .append("svg:path") // (4)
  .attr("d", function(d) { // (5.2)
    return d.path;
  })
  .classed('fill', function(d) {
    return d.fill
  })
  .classed('no-fill', function(d) {
    return !d.fill
  });
```

[Path Example on CodePen](http://codepen.io/pigne/pen/jbdzdo?editors=011)



### Handle events


```javascript
var user_data = [ 10, 20, 35, 22, 25, 40];

var coordiate_width = 800;
var display_width = 400;

var svg = d3.select('#svg1')
  .append("svg")
  .attr("width", display_width)
  .attr("height", display_width/2)
  .append("g")
  .attr("transform", "scale("
    +(display_width/coordiate_width)+")");

var blocks = svg.selectAll('.block')
  .data(user_data)
  .enter()
  .append("circle")
  .classed("block",true)
  .attr("cx", function(d, i) {
      return 100*(1+i);
  })
  .attr("cy", coordiate_width/4)
  .attr("r", function(d, i) {
      return d;
  })
  .on("mouseover", function(d, i){
    d3.select(this)
      .classed('over', 1);
  })
  .on("mouseout", function(d, i){
    d3.select(this)
      .classed('over', 0);
  });
```

```css
.block {
  stroke: #55F;
  stroke-width: 2px;
  stroke-linecap: square;
  fill: #EEE;
}

.block.over {
  stroke: #f99;
  stroke-width: 3px;
  fill: #DDD;
}

```

[Events Example](http://codepen.io/pigne/pen/JBzai)

### Fonctionnement avec React et Redux

Dans une application avec React et Redux, la source unique d'information est le store redux.

On va créer des composants react connectés au store et répondre aux évènements de mise à jour du composant réact (le [cycle de vie du composant](http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)):

- `componentDidMount`
- `componentDidUpdate`
- `componentWillUnmount`
