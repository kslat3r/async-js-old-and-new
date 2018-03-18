# Asynchronous JavaScript

## The Old & New

### March 20th 2018

---

## What we will cover

* Asynchronous JavaScript and how it works
* Callbacks
* Promises
* Generator functions
* Async/await

---?image=assets/image/queue.jpg

## Synchronous functions

---

```javascript
const random = Math.random();

// waits for line 1 to return

const low = Math.floor(random);

// waits for line 2 to return

const high = Math.ceil(random);
```

---

* Execution happens in order, one at a time
* Executed immediately by the event loop
* Blocking functions (JavaScript is single threaded)
* Default behaviour
* Happens very quickly on modern hardware

---

> Scenario: You are in a queue to get a movie ticket. You cannot get one until everybody in front of you gets one, and the same applies to the people queued behind you. - [Stack Overflow](https://stackoverflow.com/questions/748175/asynchronous-vs-synchronous-execution-what-does-it-really-mean/26804844#26804844)

---?image=assets/image/cable.jpg

## Asynchronous functions

---

```javascript
const callback = () => {
  console.log('Hello, world!');
}

setTimeout(callback, 10000);
```

---

* Can enter the stack at any time
* Non-blocking (does not delay single execution thread)
* Some functions take an unpredictable amount of time to execute e.g. network requests

---

> Scenario: You are in a restaurant with many other people. You order your food. Other people can also order their food, they don't have to wait for your food to be cooked and served to you before they can order. In the kitchen restaurant workers are continuously cooking, serving, and taking orders. People will get their food served as soon as it is cooked. - [Stack Overflow](https://stackoverflow.com/questions/748175/asynchronous-vs-synchronous-execution-what-does-it-really-mean/26804844#26804844)

---

### However

* Getting your order in before your neighbouring table does not guarantee your food will come first.

---

### How does this work?

---

### Another analogy

* Imagine the bad friend you invite to your party: you can’t rely them to show up on time, so you start the party without them.
* But what if your friend is bringing the drinks? We might have to wait!

---?image=assets/image/problem.jpg

## The problem

---

### Question

How long will this code take to execute from start to finish?

```javascript
setTimeout(() => console.log(1), 1000);
setTimeout(() => console.log(2), 2000);
setTimeout(() => console.log(3), 3000);
```

---

### Answer

3 seconds

---

* All timers start at near enough the same time
* But what if we want to start our timers one after another?

---?image=assets/image/telephone.jpg

## Solution one - nested callbacks

---

```javascript
setTimeout(() => {
  console.log(1);

  setTimeout(() => {
    console.log(2);

    setTimeout(() => {
      console.log(3);
    }, 3000);
  }, 2000);
}, 1000);
```

---

* Seems simple on the surface, but quickly becomes messy.
* Hard to read.
* Hard to maintain.

---

### Callback hell

![Callback hell](assets/image/callbackhell.png)

---?image=assets/image/promise.jpg

## Solution two - Promises

---

```javascript
const timeout = (ms) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve();
    }, ms);
  });
});

timeout(1000)
  .then(() => {
    console.log(1);

    return timeout(2000);
  })
  .then(() => {
    console.log(2);

    return timeout(3000);
  })
  .then(() => {
    console.log(3);
  });
```

---

* A promise is a proxy for a value
* Essentially an object with a `.then()` method that takes a callback function
* The callback fires when the Promise's `resolve()` function fires

--- 

### Promise chaining

```javascript
  timeout(1000)
    .then(timeout(2000))
    .then(timeout(3000))
    .then(...
```

---

* Allows you to view your callbacks in a more friendly way
* Lots of other useful methods including Promise.all()
* Huge ecosystem of libraries e.g. `bluebird`

---

### However

* Still susceptible to callback hell
* Lots of boilerplate `.then()`, `.catch()` and `.finally()` code

---?image=assets/image/generator.jpg

## Solution three - Generator functions

---

```javascript
const timeout = function* (ms) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve();
    }, ms);
  });
});

yield timeout(1000);
console.log(1);

yield timeout(2000);
console.log(2);

yield timeout(3000);
console.log(3);
``` 

---

* Part of the ES6 specification
* First available in Node.js v0.12.18 (behind a runtime flag)
* Can be used as an iterator function e.g.

```javascript
const example = function* () {
  yield 1;
  yield 2;
  yield 3;
};

const iterator = example();

console.log(iterator.next()); // {value: 1, done: false}
console.log(iterator.next()); // {value: 2, done: false}
console.log(iterator.next()); // {value: 3, done: true}
```

---

### Try/catch

```javascript
const timeout = function* (ms) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject('Example error');
    }, ms);
  });
});

try {
  yield timeout(10000);
} catch (e) {
  console.log(e);
}
```

---?image=assets/image/wait.jpg

## Solution four - Async/await

---?image=assets/image/information.jpg

## More information