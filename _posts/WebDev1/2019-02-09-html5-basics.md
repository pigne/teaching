---
layout: post
title: HTML5 Basics
categories:
- WebDev1
- lecture
author: Yoann Pigné
published: false
---

<!-- TOC depthFrom:2 depthTo:2 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Markup](#markup)
- [Document Object Model (DOM)](#document-object-model-dom)
- [Events](#events)
- [Asynchronous Communication](#asynchronous-communication)

<!-- /TOC -->

## Markup

### A HTML5 Template (from [html5bones](https://html5bones.com/))


```html
<!DOCTYPE html>
<html lang="fr"> 
<head>
    <meta charset="utf-8">
    <title>PAGE TITLE</title>
    <meta name="description" content="">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
    <link href="css/normalize.css" rel="stylesheet" media="all">
    <link href="css/styles.css" rel="stylesheet" media="all">
</head>
<body>
    <header role="banner">
        <h1>TITRE</h1>
        <nav role="navigation">
            <!-- list ou paragraphe contenant les liens internes de l'application -->
        </nav>
    </header>
    <div class="wrap">
        <main role="main">
          <!-- des 'sections', des 'articles' avec navigation interne, entêtes et pieds -->
        </main>
        <aside role="complementary">
          <!-- Contenu non essentiel au contenu principal.  -->
        </aside>
    </div>
    <footer role="contentinfo">
        <address>
          <p>Pour plus d'information, contactez <a href="mailto:admin@example.com">Quelqu'un</a>.</p>
        </address>
        <small>Copyright &copy; <time datetime="2017">2017</time></small>
    </footer>
</body>
</html>

```

Note : `role` attributes are used for accessibility purposes as per [WAI-ARIA](https://www.w3.org/TR/wai-aria/).

### Sections

Sections are semantic replacements for the generic `div` tag.

#### `<main>`

Main visible content of the web page. Does not contribute to the outline.

#### `<article>`

Self-sufficient content that can be exported as is.

#### `<section>`

Coherent piece of information. Part of a larger content.

#### `<header>`

Introduce an article, a section, or the whole page.

#### `<footer>`

Conclude an article, a section, or the whole page.

#### `<aside>`

Content related (but not central) to an article, a section, or the whole page.

#### `<nav>`

List of navigation links to other contents or parts of the site.

#### `<hgroup>`

Group of titles (h1, h2, h3, ...).


### Give semantic to Web pages sectioning

![Markup Sectioning]({{ site.baseurl }}/images/sections.svg)

### Possible Article Sectioning

![Article Sectioning]({{ site.baseurl }}/images/article.svg)


### Grouping content

#### `<figure>`

Piece of information (mostly graphical) from an article that can be place at different positions.

#### `<figcaption>`

Caption of a figure.

### Interactive elements

#### `<details>`

Control for additional on-demand information.

#### `<summary>`

Summary, caption, or legend for a details control.

### Embedded Content

#### `<video>`

Embed videos in the page.

```html
<video src="videofile.ogg" autoplay poster="posterimage.jpg"></video>
```

Possible attributes:

- `autoplay`
- `buffered`
- `controls`
- `crossorigin`
- `height`
- `loop`
- `muted`
- `played`
- `preload`
- `poster`
- `src`
- `width`

#### `<audio>`

Represent sound content in the document.

```html
<audio src="audio.ogg" autoplay>
```

#### `<source>`

Specify alternate sources for `audio` and `video`.

```html
<video controls>
  <source src="foo.ogg" type="video/ogg">
  <source src="foo.mp4" type="video/mp4">
  <source src="foo.webm" type="video/webm">
  Your browser does not support the <code>video</code> element.
</video>
```

#### `<track>`

Specify subtitles for `audio` and `video`.

```html
<video src="foo.ogg">
  <track kind="subtitles" src="foo.en.vtt" srclang="en" label="English">
  <track kind="subtitles" src="foo.sv.vtt" srclang="sv" label="Svenska">
</video>
```

#### `<mathml>`

Mathematical formula. MathML format.

<math>
<mrow>
<mrow>
<msup><mi>a</mi><mn>2</mn></msup>
<mo>+</mo>
<msup><mi>b</mi><mn>2</mn></msup>
</mrow>
<mo>=</mo>
<msup><mi>c</mi><mn>2</mn></msup>
</mrow>
</math>

```html
<math>
<mrow>
<mrow>
<msup><mi>a</mi><mn>2</mn></msup>
<mo>+</mo>
<msup><mi>b</mi><mn>2</mn></msup>
</mrow>
<mo>=</mo>
<msup><mi>c</mi><mn>2</mn></msup>
</mrow>
</math>
```


[demos on CodePen](http://codepen.io/pigne/pen/epKpYV).


#### `<progress>`

Completion progress of a task.


<progress value="70" max="100">70 %</progress>



```html
<progress value="70" max="100">70 %</progress>
```



### Forms

#### `<datalist>`

A set of predefined options for other controls.

```html
Browsers: <input list="browsers">
<datalist id="browsers">
<option value="Chrome">
<option value="Firefox">
<option value="Internet Explorer">
<option value="Opera">
<option value="Safari">
</datalist>
```

#### `<output>`

Field for the result of a calculation.

<form oninput="result.value=parseInt(a.value)
            +parseInt(b.value)">
<input type="number" name="b"  /> +
<input type="number" name="a"  /> =
<output name="result"></output>
</form>

```html
<form oninput="result.value=parseInt(a.value)
            +parseInt(b.value)">
<input type="number" name="b"  /> +
<input type="number" name="a"  /> =
<output name="result"></output>
</form>
```



#### `<meter>`
Graphical representation of a measure.


<meter min="0" max="30" low="5" high="20"
                value="15">
  15 degrees.
</meter>

```html
<meter min="0" max="30" low="5" high="20"
                value="15">
  15 degrees.
</meter>
```


### Forms new attributes

#### `novalidate`

Prevent validation of the form before sending it.

### Forms new inputs control types

#### `search`

<input type="search" name="my_search">

```html
<input type="search" name="my_search">
```


#### `number`

<input type="number" name="my_number">

```html
<input type="number" name="my_number">
```


#### `range`

<input type="range" name="my_range">

```html
<input type="range" name="my_range">
```


#### `color`

<input type="color" name="my_color">

```html
<input type="color" name="my_color">
```


#### `date`

<input type="date">

```html
<input type="date">
```


Other dates and times: `datetime datetime-local month time week`




### Forms inputs attributes

#### `form`

Specifies which form this inputs belongs to (since inputs do not **have** to be placed inside forms)

Other forms' attributs inside inputs :  `formaction`, `formenctype`, `formmethod`, `formnovalidate` and  `formtarget`. These attributes override the form's default `action`, `enctype`, `novalidate` and `target` attributes.

```html
<form action="default_form.php">
   <input type="submit" value="Default Submit" /><br />
   <input type="submit" formaction="alternate_form.php" value="Alternate Submit" />
</form>
```

#### `multiple`

Allow more than one value in the input.


#### `placeholder`

A hint for the user before he starts typing. It is **not** a default value.

<input type="text" placeholder="login">
<input type="password" placeholder="password">

```html
<input type="text" placeholder="login">
<input type="password" placeholder="password">
```

#### `min`, `max`, `step`


<input type="range" min="12" max="120" step="12"><br>
<input  type="number" min="12" max="120" step="12"><br>
<input  type="date" min="2020-01-01" max="2025-06-31" step="14">



```html
<input type="range" min="12" max="120" step="12"><br>
<input  type="number" min="12" max="120" step="12"><br>
<input  type="date" min="2020-01-01" max="2025-06-31" step="14">
```


#### `pattern`

Regular expression to validate an input.

```html
<input type="text" name="code" pattern="[A-Za-z]{3}" title="3 letters code" />
```

#### `required`

validation of the input. Will raise a message if no value on required input.

```html
<form action="#">
  code: <input type="text" name="code" pattern="[A-Z]{3}" placeholder="AAA" title="Three letters code" autofocus />
  <br>
  value: <input type="number"  min="1" max="100" title="integer between 1 and 100" required>
  <input type="submit">
</form>
```



### New global attributes

#### `data-*`

User defined attributes. Any attribute prefixed by "_data-_" can be defined and used. HTML pages with `data-*` attributes are valid pages.

#### `on*`

Specify code to execute on specific _events_ linked to an element:

`onabort`, `onblur`, `oncancel`, `oncanplay`, `oncanplaythrough`, `onchange`, `onclick`, `onclose`, `oncontextmenu`, `oncuechange`, `ondblclick`, `ondrag`, `ondragend`, `ondragenter`, `ondragleave`, `ondragover`, `ondragstart`, `ondrop`, `ondurationchange`, `onemptied`, `onended`, `onerror`, `onfocus`, `oninput`, `oninvalid`,
 `onkeydown`, `onkeypress`, `onkeyup`, `onload`, `onloadeddata`, `onloadedmetadata`, `onloadstart`, `onmousedown`, `onmousemove`, `onmouseout`, `onmouseover`, `onmouseup`, `onmousewheel`, `onpause`, `onplay`, `onplaying`, `onprogress`, `onratechange`, `onreset`, `onscroll`, `onseeked`,
 `onseeking`, `onselect`, `onshow`, `onstalled`, `onsubmit`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`

#### `accesskey`

Define keyboard shortcuts.

#### `class`

List of classes this element belongs to.

#### `contenteditable`

Boolean. Is this element is editable?

#### `contextmenu`

Link a `command` to a menu.

#### `dir`

Set the direction of the text in that element.

#### `draggable`

Boolean. Is that element draggable?

#### `dropzone`

What action to take when an element is dropped on this one (copy, move, link...)

#### `hidden`

Boolean. Is it visible?

#### `id`

The unique identifier of an element.

#### `lang`

Primary language of the text in that element.

#### `spellcheck`

Boolean. Activate spell checking?

#### `style`

CSS element style.

#### `tabindex`

Integer that defines the order of focus of the element with the keyboard.

#### `title`

Tooltips...

#### `translate`

Boolean. Does the content of the element need to be translated when the page is localized?

## Document Object Model (DOM)

Structural representation of a HTML or XML document. Content and visual presentation can be modified with JavaScript.

- Each web page loaded in the browser has a `document` object. It is the entry point of the web page content. The root of the DOM tree.
- In a document's script, `document` is also accessed through `window.document`.
- Each Element in the DOM inherits from the `Node` Interface.
- Specialized elements have other interfaces:
      - attributes inherit from `Attr`
      - elements inherit from `Element`
      - `document` inherit from `Document`

### The `Node` interface

#### Attributes

##### `nodeName`

Represents the name of an elements. Of an Element, `nodeName`  is the `tagName`, for text it is the value "#text", for an attribute it is its name.

##### `nodeValue`

- string value of attributes
- text string of text nodes
- `null` for other objects

##### `attributes`

List of attributes. Only for elements.

##### `parentNode`

Link to the parent node.

##### `childNodes`

List of child nodes, if any.

##### `firstChild`

First child in the `childNodes` list.

##### `lastChild`

Last child in the `childNodes` list.

##### `previousSibling`

Previous node at the same level of this node.

##### `nextSibling`

Next node at the same level of this node.


#### Methods

- `appendChild(newChild)`
- `cloneNode(deep)`
- `hasAttributes()`
- `hasChildNodes()`
- `insertBefore(new,ref)`
- `removeBefore(old)`
- `replaceChild(new,old)`



### The `Document` interface

#### Attributes

- `documentElement`: the html Element.
- `documentURI`: the document's location.
- `head` and `body`:  the 2 html elements.
- `cookie`: semicolon-separated list of the _cookies_ for that document.

#### Methods

- `createElement(tagName)`: create new DOM elements.
- `createTextNode()`: create text nodes.
- `createEvent()`: create events.


### Elements access through the `Document` and `Element` Interfaces

- `getElementById(id)`
- `getElementsByClassName(className)`
- `getElementsByTagName(tagName)`
- `querySelector(CSSSelector)`
- `querySelectorAll(CSSSelector)`

## Events

The interaction between the user and the Web page or between various components in that page lead to events that in fact are  JavaScript function calls.


- Events define the behavior of the page.
- Each element in the page can generate events.
- Reaction to events is made through JavaScript callback functions (listeners).
- When an event is activated, the registered callbacks to this event are triggered (executed).

```javascript
function initEventHandlers() {
  document.getElementById('mainForm').addEventListener('submit', checkForm, false);
  document.getElementById('helpPopupLink').addEventListener( 'click' , popupHelp, false);
}
window.addEventListener( 'load', initEventHandlers, false);
```

### Attribute events are **_bad_**

```html
<body onload="initPage()">
<!-- ... -->
<form method="post" action="p.php" onsubmit="checkForm()"> ...
<!-- ... -->
<a href="#" onclick="return popupWindow('h.htm')">Aide</a> ...
<!-- ... -->
<div onclick="doSomething()"> ...
```

- Mixing of content and behavior.
- Multiple bindings on one event not allowed.
- Only a subset of events are supported.

### Events Bubbling/Capturing

When an event is fired, the DOM tree is traversed twice. First from the root to the element firing the element. Then from that element back to the root of the DOM.

The first traversal from the root to the element is called _capturing_. The second traversal (from the element to the root) is called _bubbling_.

An event, when registered, will be triggered during capturing or bubbling (bubbling by default) depending on the third parameter of the `addEventListener` method:

```javascript
otherElement.addEventListener( 'click', respondToClick , true); // use capture
someElement.addEventListener( 'focus', someSpecialBehaviour , false); // use bubbling
window.addEventListener( 'load', something); // use bubbling
```

The normal course of doth traversals may be stopped by any event handler with `event.stopPropagation()`.

[A complete event traversal example on CodePen](http://codepen.io/pigne/pen/meLGRp).

### Default Events

During an event some default behavior is intended (e.g. clicking a radio button should change it UI, etc.). Default behaviors in response to events can be controlled. An event can be prevented from happening without preventing event propagation (capturing/bubbling). `event.preventDefault()` can prevent the default behavior of an element upon the considered event.

## Asynchronous Communication

### XMLHttpRequest
A JavaScript object that allows the exchange of information between the client and the server, _without_ reloading the page.


The object is standardized and mostly implemented everywhere.

```javascript
let xhr;
if( typeof XMLHttpRequest != "undefined" ) {
  xhr = new XMLHttpRequest();
  xhr.onreadystatechange = handleData;
  xhr.open('GET', 'test.html');
  xhr.send();
}
```

2 important methods:

- `open()`: sets parameters, initializes the connection.
- `send()`: actually send the request:

XMLHttpRequest are **asynchronous**. 5 states are passed after sending:

| value | name | comment |
|---|---|---|
| 0 | `UNSENT` | open() has not been called yet. |
| 1 | `OPENED` | open() has been called. |
| 2 | `HEADERS_RECEIVED` | send() has been called, and headers and status are available. |
| 3 | `LOADING` | Downloading; responseText holds partial data. |
| 4 | `DONE` | The operation is complete. Data is available in responseText. |

 How do we get the data?

```javascript
var xhr;
var data;
//...
xhr.open( "GET", "/message" ); // method and URL
xhr.onreadystatechange = handleData;
xhr.send( null ); // parameter only used to send  POST data
//...
function handleData () {
  if ( xhr.readyState === 4 ) { // Data received.
    if( xhr.status === 200 ) { // HTTP status OK.
      data = xhr.responseText;
    }else{
      // deal with the server error...
    }
  }
}
```
