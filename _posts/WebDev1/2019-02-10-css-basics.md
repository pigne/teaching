---
layout: post
title: Cascading Style Sheets (CSS) basics
categories:
- WebDev1
- lecture
author: Yoann Pigné
published: true
---

Describe the presentation of a document written in HTML or XML (including XHTML, SVG, ...)

- CSS level 1: fisrt level of the norm. Obsolete.
- CSS level 2.1: legacy level.
- CSS level 3: new modular approach. Many new features.
- CSS snapshots 2007, 2010, 2015, 2017 



- [CSS Basic building blocks](#css-basic-building-blocks)
- [Specificity](#specificity)
- [The Box Model](#the-box-model)
- [Positioning](#positioning)
- [Media Queries](#media-queries)
- [Get Extra Information](#get-extra-information)
- [Vendor Prefixes](#vendor-prefixes)
- [Focus on Some Recent Features](#focus-on-some-recent-features)
- [CSS Pre-processors <small>Dynamic CSS</small>](#css-pre-processors-smalldynamic-csssmall)


## CSS Basic building blocks

- The **property**: a name that identifies a visible feature (color, position, decoration).
- The **value**: a description of how the property should be painted.
- Comments :

```css
/* multiline-only c-style comments */
```

### CSS Declarations

A declaration assigns a value to a property. The property and the value are separated by the `:` character.

```css
color: blue
```

### CSS Declarations Blocks

Declarations are grouped in blocks. A block opens with a `{` and closes with a `}`.
Declarations are separated with a `;`.

```css
{
  color : darkblue ;
  background-color : #EFF4F5
}
```

### CSS Selectors

Style blocks may apply to a subset of the DOM, not to all the elements of the page. A selector is a condition statement that matches a selection of elements.

```css
#mainmenu, div.menu > ul , nav ul
```

Selection can be based on ids (`#myId`), classes (`.myClass`) tags (`div`), pseudo-classes (`:hover`), attributes (p[hidden="true"]), or with general selectors(*[hidden="true"]).

Search by inheritance is done strictly (direct descendant of) with `>` or loosely (some descendant of) with a "space".

### CSS Rulesets (or Rules)

A ruleset is the combination of a selector with a block of declarations, where matched elements are applied the declarations in the block.

```css
#mainmenu, div.menu > ul , nav ul {
  color : darkblue ;
  background-color : #EFF4F5
}
```

### At-Rules

Statements that start with an at sign `@` followed by an identifier. Then the rest of the statement is up to the next `;` (if no block) or the end of a block `}` (if there is a block). The syntax of the rest may differ for each at-rule.

```css
@charset "UTF-8";  /* Set the encoding of the style sheet to UTF-8 */
@import url("fineprint.css"); /* Import rules from another file */
@font-face {
  font-family: MyHelvetica;
  src: local("Helvetica Neue Bold"),
  local("HelveticaNeue-Bold"),
  url(MgOpenModernaBold.ttf);
  font-weight: bold;
}
```


## Specificity

The order the navigator chooses to use to select the good rules for the elements.

Order of specificity (by less specific to highly specific):

- Universal selectors (e.g.: `*`)
- Type selectors (e.g.: `div`)
- Class selectors (e.g.: `.my-class`)
- Attributes selectors (e.g.: `a[href*="example"]`)
- Pseudo-classes (e.g.: `p:nth-child(4n+1)`)
- ID selectors (e.g.: `#item234`)
- Inline style(e.g.: `<p style="color:red; width:100%;">.../<p>`)

### The `!important` exception

When `!important` is added at the end of a declaration, this one overwrites **all** the others made in the CSS for this property with these elements.

## The Box Model

Each element is represented as a 4-parts box model.

- The **content**: controlled with `width`, `min-width`, `max-width`, `height`, `min-height`, `max-height`
- The **padding**: controlled with `padding-top`, `padding-right`, `padding-bottom`, `padding-left`
- The **border**: controlled with `border-width`
- The **margin**: controlled with `margin-top`, `margin-right`, `margin-bottom`, `margin-left`

![CSS Box Model @ [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model)]({{ site.baseurl }}/images/boxmodel.png)


## Positioning

The `position` property defines how a block should be positioned in regard to the other blocks in the page with properties such as `top`, `left`, `right` and `bottom`.

There are 5 possible positioning strategies for the `position` property.



### `static`

The default behavior. Blocks are positioned one after the other following the normal flow of the DOM tree.



### `relative`

Position relative to initial position in the flow. When a block is  relatively positioned, it's static position and place used is reserved (a gap is created) and the block is positioned relatively to this initial position (with `top`, `left`, `right` and `bottom` properties) block still occupies space on the normal flow Leaves a gap at original position.

![CSS Relative Positioning from [MDN](https://developer.mozilla.org/fr/docs/Web/CSS/position)]({{ site.baseurl }}/images/relative-positioning.png)

### `absolute`

Specify position relative to its closest **positioned** ancestor or to the containing block. Does not leave space for the element in the normal flow.

![CSS Absolute Positioning from [MDN](https://developer.mozilla.org/fr/docs/Web/CSS/position)]({{ site.baseurl }}/images/absolute-positioning.png)


### `fixed`

Specify position relative to the **screen**'s viewport. Does not leave space for the element in the normal flow.

![CSS Fixed Positioning from [MDN](https://developer.mozilla.org/fr/docs/Web/CSS/position)]({{ site.baseurl }}/images/fixed-1.png)
![CSS Fixed Positioning from [MDN](https://developer.mozilla.org/fr/docs/Web/CSS/position)]({{ site.baseurl }}/images/fixed-2.png)

### `sticky` <small>CSS 3 Draft 10-2015</small>

Mix between `relative` et `fixed` positioning. Behaves like the relative positioning up until it would normally get out of the viewport, then fixed positioning occurs.

:warning: vérifier le support:  <http://caniuse.com/#search=sticky>

[CSS Position Demo on CodePen.](http://codepen.io/anon/pen/YyvVPL)


## Media Queries

Limit the stylesheet's scope with media types and other properties

```css
@media media-types | media-features {
  /* media-specific rules */
}
```

### media types <small>CSS 2.1</small>

`all`, `speech`, `braille`, `handheld`, `print`, `projection`, `screen`, `tty`, `tv`, `embossed`

### Media Features <small>CSS 3</small>

`height`, `min-height`, `max-height`, `device-width`, `min-device-width`, `max-device-width`, `device-height`, `min-device-height`, `max-device-height`, `aspect-ratio`, `min-aspect-ratio`, `max-aspect-ratio`, `device-aspect-ratio`, `min-device-aspect-ratio`, `max-device-aspect-ratio`, `color`, `min-color`, `max-color`, `color-index`, `min-color-index`, `max-color-index`, `monochrome`, `min-monochrome`, `max-monochrome`, `resolution`, `min-resolution`, `max-resolution`, `scan` and `grid`.


### Logical operators <small>CSS 3</small>

`and`, `,`, `not` and `only`

### Examples

```css
@media (max-width: 500px), handheld and (orientation: portrait) {
  aside.adds {
    display: none;
  }
}
@media only print and (max-device-width:800px) {
  body {
    background-color:white;
    color:black
  }
}
```

## Get Extra Information

The most reliable and up to date source of information about CSS: **The Mozilla Developer network**.

[developer.mozilla.org/en-US/docs/CSS](https://developer.mozilla.org/en-US/docs/CSS)


Check browsers availability for new  features on <http://caniuse.com>

## Vendor Prefixes

Browsers consider some properties with a **prefix** when standards are not stabilized and the feature's behavior may change, or when the implementation of a feature is not stabilized in the browser. The code and the syntax may change.

```css
selector {
  -webkit-property : value;
  -moz-property : value;
  -ms-property : value;
  -o-property : value;
  property : value;
}
```

Check needed prefixes on (http://caniuse.com/) or use [Autoprefixer](https://github.com/ai/autoprefixer).



## Focus on Some Recent Features

- `transition`: change value of a property with a smooth animation
- `transform`: apply 2D transformations to elements
- `animations`: define animation of elements





### The CSS `transition` properties

Permit a smooth transition between an **old** and a **new** value of a CSS property. CSS properties are changed by:

- pseudo classes: `:hover`, `:focus`, `:active`
- adding/removing a class to an element via JavaScript:

```javascript
for(let element of document.getElementsByTagName('a')) {
  element.className="link";
}
```

- dynamic styling via JavaScript:

```javascript
document.getElementById("d").style.color = "orange";
```

4 properties:

- transition-property: List of CSS properties to transform.
- transition-duration: Duration of the smooth effect.
- transition-timing-function (optional): Transition function (acceleration, trajectory, deceleration).
- transition-delay (optional): Duration to wait for before starting the transition.


#### A transition example

```css
#test_transition:hover {
transition-property: font-size, background-color, color;
transition-duration: 2s, 2s, 10s;
transition-timing-function: ease;
transition-delay: 0s;
font-size: 2em;
background-color: #333;
color:#eee;
}
```

[Transition Demo on CodePen](http://codepen.io/pigne/pen/qOyZQo)


### Short notation may help

```css
  transition:
      property
      duration
      timing-function
      delay;
```
example:

```css
transition: font-size 2s ease 0s, background-color 2s ease, color 8s;
```


### The CSS `transform` property

Move HTML elements on the X and Y axis.

2 properties:

- `transform`: defines the list of function that generate the transformation

```css
transform : function1(value1)
  function2(value2)
  function3(value3);

```

- `transform-origin` defines the origin point (x,y) relative to the top left corner of the element, from which the transform function will be computed

```css
transform-origin: 50% 50%;
transform-origin: top 0 left 0;
```

Does `transform` need [vendor prefixes](http://caniuse.com/#feat=transforms2d)? Not anymore!

```css
#transform_test {
  transform-origin: 50% 50%;
  transform:  scale(1.2)
        rotate(15deg)
        translate(100px, 0px);
}
```

[Simple transform test on CodePen](http://codepen.io/pigne/pen/GpBqpV)


### Mixing `transform` and `transition`

[Full example on CodePen.](http://codepen.io/pigne/pen/tIfrc)

```css
#transform_test:hover {
  transition-property: transform, background-color;
  transition-duration: 2s;
  background-color: red;
  transform: scale(.7) rotate(-1455deg) translate(0px, 0px);
}
```

### More control with CSS `animation`

Define property values at key moments of an animation (_keyframes_). Animations can be repeated.

Several properties:
- `animation-name`
- `animation-duration`
- `animation-iteration-count`
- `animation-direction`
- `animation-timing-function`
- `animation-delay`
- `animation-fill-mode`

#### Example

[Full Example on CodePen](http://codepen.io/pigne/pen/ErgGL)

```html
<style>
#animation_test {
  height: 200px;
  width: 200px;
  border: 1px solid rgba(0,0,0,0.1);
  position:relative;
  border-radius: 50%;
}
#animation_test:hover {
  animation: rainbow 15s 15 linear;
}
@keyframes rainbow {
  0% {background-color: #FF0000;}
  10% {background-color: #FF8000;}
  20% {background-color: #FFFF00;}
  30% {background-color: #80FF00;}
  40% {background-color: #00FF00;}
  50% {background-color: #00FF80;}
  60% {background-color: #00FFFF;}
  70% {background-color: #0080FF;}
  80% {background-color: #0000FF;}
  90% {background-color: #8000FF;}
  100% {background-color: #FF0080;}
}
</style>

<div id="animation_test" class="third" ></div>
```

## CSS Pre-processors <small>Dynamic CSS</small>

CSS is a limited description language: no function, no inheritance, no arithmetic and no variables until recently.

### CSS variables

- declare :

```CSS
element {
  --main-bg-color: brown;
}
```

- use:

```CSS
element {
  background-color: var(--main-bg-color);
}
```


<https://developer.mozilla.org/fr/docs/Web/CSS/Les_variables_CSS>



- Numerous pre-processing tool give new functionalities to css: new languages
  - [LESS](http://lesscss.org/)
  - [SASS](http://sass-lang.com/)
  - [Stylus](http://learnboost.github.io/stylus/)
  - ...


### SASS' Main features (SCSS syntax)

#### Variables

values used in various declarations like themed colors, default values for various sizes (border, padding, margin, spaces, ...)

```css
$main-theme-color-bg: #428bca;
body{
  background-color: $main-theme-color-bg;
}
```

#### Mixins

Inherit properties of a class into another declaration block.

```css
@mixin general_stuff{
  /* ... */
}
#special_item {
  @include general_stuff;
  /* ... */
}
```

#### Nested Rules

more expressive inheritance and shorter code.

```css
#menu {
  h1 {
    /* ... */
  }
  ul{
    /* ... */
  }
}
```

#### Mixins with parameters

```css
@mixin big-font ($size: 18px) {
  font-size: $size;
  font-weight: 700;
}
.important {
  @include big-font;
}

#more-important {
  @include big-font(25px);
}
```

#### Functions and Operations

Mostly predefined functions. Operations are used within parenthesis.

```css
.something {
  width : ($default-width * 1.3);
  color : darken($default-color, 3%);
  background-color: desaturate($default-color, 10%);
}
```
