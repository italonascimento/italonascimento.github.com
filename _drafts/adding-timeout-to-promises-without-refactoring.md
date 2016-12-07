---
layout: post
title: "Adding timeout to Promises without refactoring"
description: "Quick guide on how to implement a generic timeout with Promise.race and how to apply it in previous existing promises."
date: 2016-11-30
tags: [javascript, promise, timeout, promise.race]
comments: true
share: true
---

Sometimes a promise may take too long to resolve or reject, and sometimes we may just not want to wait for it.

Take a look at this code snippet, for example:

```javascript
const doSomething = function(){
  return new Promise((resolve, reject) => {
    /*
    TODO: try to do something asynchronously
    and resolve or reject according to
    operation result.
    */
  })
};

// Call doSomething and receive a Promise as return
let doIt = doSomething()

// Wait for the promise to get resolved...
doIt.then(response => {
  // Use response
})

// ... or to get rejected
doIt.catch(error => {
  // Deal with error
})
```

It's a powerful pattern, for sure, but doesn't give us much control, right? All we can do is to wait for `doSomething` to do whatever it needs, and to finally resolve/reject and fires our callbacks.

Now, suppose a scenario in which `doSomething` can't take more than 5 seconds to get resolved. What could we do?

## Using Promise.race

Even though there's no timeout support in JavaScript native promises, it's really straight forward to implement such functionality using an alternative method: `Promise.race`.

As stated in [MDN](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise/race):

> The **Promise.race(iterable)** method returns a promise that resolves or rejects as soon as one of the promises in the iterable resolves or rejects, with the value or reason from that promise.

Looking at it in code:

```javascript
let promiseA = new Promise((resolve, reject) => {

  // Will resolve after 200ms
  let wait = setTimeout(() => {
    clearTimout(wait);
    resolve('Promise A win!');
  }, 200)
})

let promiseB = new Promise((resolve, reject) => {

  // Will resolve after 400ms
  let wait = setTimeout(() => {
    clearTimout(wait);
    resolve('Promise B win!');
  }, 400)
})

let race = Promise.race([
  promiseA,
  promiseB
])

race.then((res) => console.log(res)) // -> Promise A win!
```

Could you picture a solution already? We could even say taht `promiseA` is acting as a timeout of 200ms in this case, but not quite, as we would except a timeout to reject our promise, not to resolve.

So let's get our hands dirty and code it properly!

## Implementing the timeout

First of all, I'll add a `timeout` function to the `prototype` object of `Promise`:

```javascript
Promise.prototype.timeout = function(ms){
  /* ... */
}
```

You may prefer not to mess with the `prototype` of a native class like this, and I totally understand. But I'm taking the Q library syntax as reference here, and it can be really handy:

```javascript
doSomething().timeout(1000).then(res => console.log(res));
```

This way, by switching `doSomething()` for `doSomething().timeout(<ms>)` we'll able to specify a timeout to any promise in our project without refactoring.

Now, let's create the actual logic:

```javascript
Promise.prototype.timeout = function(ms){

  // Create a promise that will reject within <ms> miliseconds
  let timeout = new Promise((resolve, reject) => {
    let timeout = setTimeout(() => {
      clearTimeout(timeout);
      reject('Timed out after '+ ms + 'ms.')
    }, ms)
  })

  // Return a race between the original promise and the timeout
  return Promise.race([
    this,
    timeout
  ])
}
```

## The result

```javascript
const doSomething = function(){
  return new Promise((resolve, reject) => {
    /* ... */
  })
};

// Return a promise with a timeout of 5s
let doIt = doSomething().timeout(5000)

doIt.then(response => {
  // Use response
})

doIt.catch(error => {
  // Deal with error or timeout
})
```

In this new snippet, if `doSomething` doesn't get resolved in less than 5 seconds, it will get rejected.

Any doubts or considerations? Feel free to leave a comment or to reach me by e-mail anytime. :)
