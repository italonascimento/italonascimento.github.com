---
layout: post
title: "Adding timeout to Promises without refactoring"
description: "Quick tip on how to implement a generic timeout that's compatible with existing promises"
date: 2016-11-30
tags: [ui, ux, css, animation]
comments: true
share: true
---

Take a look at this code snippet:

```javascript
const doSomething = function(){
  return new Promise((resolve, reject) => {
    /*
    Try to do something asynchronously
    and resolve or reject according to
    operation result.
    */
  })
};

let doIt = doSomething()

doIt.then(response => {
  // Use response
})

doIt.catch(error => {
  // Deal with error
})
```

It's trying to do something asynchronously: `doSomething` returns us a `Promise`, and by using `then` and `catch` methods we're able to act differently whether it succeeds (gets reolved) or fails (gets rejected).

Even though promises are a powerful pattern

In some use cases, however, `doSomething` may take too long to resolve, and we may not want to wait.
