# Asynchronous JavaScript

## The Old & New

### March 20th 2018

---

## What we will cover

* Synchronous JavaScript programming
* The history of asynchronous JavaScript
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
* Non-blocking (do not block single execution thread)
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

---?image=assets/image/telephone.jpg

## Solution one - nested callbacks

---?image=assets/image/promise.jpg

## Solution two - promises

---?image=assets/image/generator.jpg

## Solution three - generator functions

---?image=assets/image/wait.jpg

## Solution four - async/await

---?image=assets/image/information.jpg

## More information