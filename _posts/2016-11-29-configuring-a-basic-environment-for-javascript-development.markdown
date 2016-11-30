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

In this new context, to program **JavaScript** is not to create simple code snippets and to load them in HTML anymore. It is currently synonym to develop complex applications, and therefore, there are a lot more to take in concern than ever were.

## Development environment: what for?

The development of complex applications require things such as modularity and high level programming paradigms. Although, since the current widely supported version of JavaScript (ECMAScript 5) doesn't have such features out of the box, how could we use it to build anything really complex, reliable and cross platform?

The answer: by **setting up a good environment**.

There are plenty of JavaScript libraries and tools available. If we pick the right ones and successfully combine them, we can achieve amazing results, such as:

* Write code in a newer standard ([ECMAScript 6](http://es6-features.org/)), taking advantage of higher level techniques, and **compiling** down to the widely supported ECMAScript 5;
* Keep our code modular and **bundle** it up to a single file for production;
* **Manage dependencies** when using third part modules.

## How to configure a basic environment?

Let's get our hands dirty and set up a basic environment. It will include:

* Dependency management with [npm](https://www.npmjs.com/);
* ES6 compilation with [babel](https://babeljs.io/)
* Module bundling with [webpack](https://webpack.github.io/)
