---
layout: post
title: "Applying a timeout to your promises"
description: "Quick tutorial on how to create a generic timeout function that can be applied to any previous existing promise."
date: 2016-12-08
tags: [javascript, promise, timeout, race]
comments: true
share: true
---

Sometimes a promise may take too long to resolve or reject, and sometimes we just can't wait for it.

There are many use cases in which this may apply, and you may have faced such scenario at least once in your coding life. I won't focus in any specific situation here, instead I'll try to show you a way (not the only one) to define a generic timeout that can be applied to any promise in your project, without any need to refactor the promise itself - in case you're receiving it from a third part module, for example.

## The problem

Take a look at this snippet:

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

It's a powerful pattern, for sure, but doesn't give us much control, right? All we can do is to wait for `doSomething` to do whatever it needs, and to finally resolve/reject and fire our callbacks.

Now, let's suppose a scenario in which `doSomething` takes too long to resolve or reject. Maybe it's trying to reach a server through a poor connection, or to parse a truly big file, doesn't matter. The important thing is that our application can't wait more than 5 seconds for a response, and if `doSomething` takes more than that, we need it to timeout and reject, firing our `catch` callback.

## Using Promise.race

Even though there's no timeout support in JavaScript native promises, it's really straight forward to implement such functionality using an alternative method: `Promise.race`.

According to [MDN](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise/race):

> The **Promise.race(iterable)** method returns a promise that resolves or rejects as soon as one of the promises in the iterable resolves or rejects, with the value or reason from that promise.

Here's how it looks like in code:

```javascript

// Will resolve after 200ms
let promiseA = new Promise((resolve, reject) => {
  let wait = setTimeout(() => {
    clearTimout(wait);
    resolve('Promise A win!');
  }, 200)
})

// Will resolve after 400ms
let promiseB = new Promise((resolve, reject) => {
  let wait = setTimeout(() => {
    clearTimout(wait);
    resolve('Promise B win!');
  }, 400)
})

// Let's race our promises
let race = Promise.race([
  promiseA,
  promiseB
])

race.then((res) => console.log(res)) // -> Promise A win!
```

Could you picture a solution already?

At this point we could already say taht `promiseA` is acting as a timeout of 200ms, except for a tiny detail: we would except a timeout to reject our promise, not to resolve it.

So let's get our hands dirty and code it properly!

## Implementing the timeout

```javascript
const promiseTimeout = function(ms, promise){

  // Create a promise that rejects in <ms> milliseconds
  let timeout = new Promise((resolve, reject) => {
    let id = setTimeout(() => {
      clearTimeout(id);
      reject('Timed out in '+ ms + 'ms.')
    }, ms)
  })

  // Returns a race between our timeout and the passed in promise
  return Promise.race([
    promise,
    timeout
  ])
}
```

I suppose the code above is pretty clear: I defined a `promiseTimeout` function, which takes in a time in milliseconds and a `promise`, and returns a race between the passed in and a locally defined promise, called `timeout`. The `timeout` promise, in turn, does nothing but reject in `ms` milliseconds.

As stated in the specification we read above, `Promise.race` will give us a new promise that gets resolved or rejected as soon as any of the passed promises resolve or reject.

Back to our function's scope, if `promise` doesn't resolve nor reject within `ms` milliseconds, we'll get the `timeout` rejection instead.

## Usage

To easily use our new function in a real project, let's first export it as a module by adding the `export default` keywords and saving as `timeout-promise.js`.

```javascript
export default const promiseTimeout = function(ms, promise){

  // Create a promise that rejects in <ms> milliseconds
  let timeout = new Promise((resolve, reject) => {
    let id = setTimeout(() => {
      clearTimeout(id);
      reject('Timed out in '+ ms + 'ms.')
    }, ms)
  })

  // Returns a race between our timeout and the passed in promise
  return Promise.race([
    promise,
    timeout
  ])
}
```

Now, by importing `timeoutPromise` and passing `doSomething()` as a parameter to it (instead of calling it directly), we're able to specify a timeout to `doSomething` even if we don't have access to it's source.

Take a look at the final code:

```javascript
import timeoutPromise from './timeout-promise';

const doSomething = function(){
  return new Promise((resolve, reject) => {
    /* ...  */
  })
};

// Apply a timeout of 5 seconds to doSomething
let doIt = timeoutPromise(5000, doSomething())

// Wait for the promise to get resolved
doIt.then(response => {
  // Use response
})

// Wait for the promise to get rejected or timed out
doIt.catch(error => {
  // Deal with error
})
```

And that's it.

Any doubts or considerations? Feel free to leave a comment or to reach me by e-mail anytime. :)
