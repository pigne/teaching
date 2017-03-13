---
layout: post
title: Tools Prerequisite
categories:
- FullStackJS
- lecture
author: Yoann Pigné
tags: js
---

Tools Prerequisite
==================

Some tools and technologies greatly help the developer's work, like code quality control, automatic formatting utilities and browser's developer tools.

Text Editor
-----------

One ***need*** a decent text editor with at least those features :

-	Syntax coloration
-	Templates Snippets
-	Linter (sea later)
-	Formatter
-	(opt) Minifier

### Some good editors

-	**Old School**: Vim, Emacs
-	**IDE**: Netbeans, Eclipse, Aptana, WebStorm
-	**Windows only**: NotePad++, UltraEdit, Dreamweaver
-	**Mac only**: TextMate, Coda, Textwrangler
-	**Linux only**: Gedit
-	:cloud: **Cloud Based**: Cloud9, Codeanywhere, Koding
-	:heart: **The ones you should use ;-)**: Sublime Text, Brackets, Atom

Web Browser Developer tools
---------------------------

A good project has to be tested on all major browsers. It is however difficult to do it during development. Tip: change your default browser every week or so.

### Dev-channels and Developer-dedicated browsers

Main browsers provide versions of browser with latest features and/or tuned for developers.

-	Chromium Dev Channel

	-	<https://www.chromium.org/getting-involved/dev-channel>

-	Chrome Canary

	-	Windows and Mac only.
	-	Uses different profiles than main Chrome/Chromium installs. You can have both installed.
	-	<https://www.google.com/chrome/browser/canary.html>

-	Firefox beta:

	-	<https://www.mozilla.org/fr/firefox/channel/#beta>

-	Firefox Developer Edition:

	-	Uses different profiles than main Firefox branch.
	-	<https://www.mozilla.org/fr/firefox/developer/>

### Developer tools

Mostly same features in all browsers (without plugin)

-	DOM/CSS analyzer/explorer
-	Network status (per request)
-	Profiler
-	Code Explorer
-	Javascript Console
-	Debugger ...

Server-side Javascript
----------------------

Appart from JS console in browsers, several interpreters are available. Node.js is a JS runtime build on Chrome's JS engine (V8).

The `node` (or `nodejs`) command is a command line interactive interpreter a.k.a. a REPL (Read–eval–print loop):

```
$ node
> var oh = {my: 'dear'}
undefined
> oh.my
'dear'
>
```

The `node` command can also run scripts (`node my_script.js`).

Node.js is associated with NPM (Node Package Manager). The NPM project manages packages written for Node.js, resolves dependencies between packages and help building new projects. NPM is also a repository where all the Node.js packages are registered at https://npmjs.org.

```
mkdir myProject && cd myProject
npm init # initialize your project!
npm search lodash
npm install --save lodash
```

### Install Node.js

On préfèrera l'installation via le gestionnaire de paquet de votre OS.
<https://nodejs.org/en/download/package-manager/>

-	On Ubuntu

```bash
sudo apt-get install curl build-essential
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install nodejs
```

-	On Windows
	-	https://nodejs.org/
	-	restart your session after install

Code Quality Control
--------------------

JavaScript is an interpreted language. No compilation. Errors are raised at runtime. A syntactic analysis usually helps. Several tools help controlling your code quality:

-	[JSLint](http://www.jslint.com/)
-	[JSHint](http://jshint.com/)
-	Google's [Closure Linter (gjslint)](https://developers.google.com/closure/utilities/)
	-	Google also publishes a [JavaSript Style Guide](https://google.github.io/styleguide/javascriptguide.xml).
- :+1: [ESLint](http://eslint.org/)

Most editor can include such tools.

Code Formatting
---------------

-	http://jsbeautifier.org/
-	your editor should format the code for you.  

Quickly Test and exchange code
------------------------------

-	General Purpose: Github's [Gist](https://gist.github.com/)

-	Specific to Web Development:

	-	[CodePen](https://codepen.io/) (Used for detailed examples in this course)
	-	[JSFiddle](https://jsfiddle.net/)
	-	[JSBin](http://jsbin.com/)

Code Versioning
---------------

Any project should be hosted on a versioning system. Free services like Github, BitBucket, GitLab, etc., are used to:

-	host public/private projects source code
-	launch automated tests
-	host projects public web sites
-	build your own portfolio

Create a profile and start building your public code portfolio.
