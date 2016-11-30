---
layout: post
title: "Configuring a basic environment for JavaScript development"
description: "An step-by-step tutorial on how to setup a basic environment to develop complex JavaScript applications."
date: 2016-11-29
tags: [javascript, npm, webpack, babel]
comments: true
share: true
---

No doubt: frontend development has changed a lot.

Since the rising of the [Web Standards](https://www.w3.org/standards/), web technologies have reached huge proportions and significantly evolved it's potential. Due to an open nature and well defined specifications, the standards spread the world and are today supported in almost any device available on the market.

In this new context, to program JavaScript is not to create simple code snippets and to load them in HTML anymore. It is currently synonym to develop complex applications, and therefore, there are a lot more to take in concern than ever were.


## Development environment: what for?

The development of complex applications require things such as modularity and high level programming paradigms. Although, since the current widely supported version of JavaScript (ECMAScript 5) doesn't have such features out of the box, how could we use it to build anything really complex, reliable and cross platform?

The answer: by **setting up a good environment**.

There are plenty of JavaScript libraries and tools available. If we pick the right ones and successfully combine them, we can achieve amazing results, such as:

* Write code in a newer standard ([ECMAScript 6](http://es6-features.org/)), taking advantage of higher level techniques, and **compile** down to widely supported ECMAScript 5;
* Keep code modular and **bundle** it up to a single file for production;
* **Manage dependencies** when using third part modules.


## Configuring a basic environment

Let's get our hands dirty and set up a basic environment. It will include:

1. Dependency management with [npm](https://www.npmjs.com/);
2. Module bundling with [webpack](https://webpack.github.io/);
3. ES6 compilation with [babel](https://babeljs.io/);
4. Task automation with [npm scripts](https://docs.npmjs.com/misc/scripts);
5. Live-reload server with [live-server](https://www.npmjs.com/package/live-server).


### 1. Dependency management with npm

First things first: before we use npm, let's make sure we have the last node.js version installed:

```console
$ node --version
$ v6.9.1
```

If you have an earlier version in your machine (or no version at all), you can grab the latest one in [node's downloads' page](https://nodejs.org/en/download/) or install it [via package manager](https://nodejs.org/en/download/package-manager/).


#### Verifying npm version

Oce node.js is installed, npm is surely also available. Let's verify it's version:

```console
$ npm --version
$ 4.0.2
```

If you've got an earlier version, you can use npm to update itself:

```console
$ sudo npm install -g npm
```

*(Omit `sudo` if you're on Windows)*


#### Starting the project

Now that node and npm are up to date, we can start a new project. Create the project folder and run the command `npm init` inside of it:

```console
$ mkdir my-project
$ cd my-project
$ mkdir src dist
$ npm init
```

This will create the following structure:

```
my-project/
    |
    ├─ dist/
    |
    └─ src/
```

Then, npm will ask a few questions about your project, for which you can type an answer or hit *enter* to go with the default. After answering the questions, a `package.json` will be created in the project root. This file contains all the information about your applications in what concerns to dependency management. It should look somewhat like this:

```json
{
  "name": "my-project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "author": "Your Name <your_email@foo.bar>",
  "license": "ISC"
}
```


#### Installing dependencies

As I stated before, for this project we're going to install webpack and babel. They are both development dependencies, which means they're required during development but not in production (as they're a bundling and a compiling tool, right?). For that reason, we're going to pass the flag `--save-dev` to npm along with the installation command:

```console
$ npm install --save-dev webpack babel-loader babael-core
```

Now they're installed under `/node_modules` and referenced in `package.json` under `devDependencies`.

We can also install production dependencies, passing along the flag `--save`. Let's take jQuery for example:

```console
$ npm install --save jquery
```

It will be referenced in `package.json` under `dependencies`.


### 2. Configuring webpack

Create the file `webpack.config.js` on the root of the project. Inside of it, paste this:

```javascript
module.exports = {
  entry: "./src/index.js",
  output: {
    path: __dirname + "/dist",
    filename: "bundle.js"
  },
  module: {
    loaders: [
      { test: /\.js$/, exclude: /node_modules/, loader: "babel-loader" }
    ]
  },
  externals: {
    "jquery": "jQuery"
  }
}
```

**Two things to notice about this setup:**

1. We're setting `babel-loader` to work on files that match `/\.js$/`, which means every JavaScript file will be processed by babel during the bundling;
2. We're declaring `jquery` as an external module, which means we're going to include it by hand and it should not be part of the bundle, what allow browsers to cache the lib.


### 3. Configuring babel

We already set webpack to process our code with babel, but it's not enough. Babel won't do anything until we setup a preset. So, let's install the latest preset available, which is actually called `latest`:

```console
$ npm install --save-dev babel-preset-latest
```

Now, create a file called `.babelrc` on the project root and past this:

```json
{
  "presets": ["latest"]
}
```


### 4. Using npm script to automate tasks

Task running is a fundamental tool during development process, and yet there are some very popular modules available to address it, npm itself offers us an straight forward approach.

Let's open our `package.json` and append some code:

```json
{
  "name": "my-project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "author": "Your Name <your_email@foo.bar>",
  "license": "ISC",
  "scripts": {
    "build": "webpack",
    "watch": "webpack --watch"
  }
}
```
