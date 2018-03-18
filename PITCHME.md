# Asynchronous JavaScript

## The Old & New

### March 20th 2018

Note:

* Discussion rather than presentation
* Ask questions at any time

---

## What we will cover

* Asynchronous JavaScript and how it works
* Callbacks
* Promises
* Generator functions
* Async/await

Note:

* But, counter-intuitively, we are going to start with

---?image=assets/image/queue.jpg

## Synchronous functions

---

```javascript
const random = Math.random();
const low = Math.floor(random);
const high = Math.ceil(random);
```

---

* Execution happens in order, one at a time
* Pushed to the call stack immediately and executed
* Blocking (JavaScript is single threaded)
* Default behaviour
* Happens very quickly on modern hardware

Note:

* Usually takes around 10 microseconds to execute

---

> Scenario: You are in a queue to get a movie ticket. You cannot get one until everybody in front of you gets one, and the same applies to the people queued behind you. - [Stack Overflow](https://stackoverflow.com/questions/748175/asynchronous-vs-synchronous-execution-what-does-it-really-mean/26804844#26804844)

Note:

* Another scenario - My boss is a busy man. He tells me to write the code. I tell him: Fine. I get started and he's watching me like a vulture, standing behind me, off my shoulder. I'm like "Dude, WTF: why don't you go and do something while I finish this?" He's like: "No, I'm waiting right here until you finish." This is synchronous.

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

* Non-blocking (does not delay single execution thread)
* Some functions take an unpredictable amount of time to execute e.g. network requests

---

> Scenario: You are in a restaurant with many other people. You order your food. Other people can also order their food, they don't have to wait for your food to be cooked and served to you before they can order. In the kitchen restaurant workers are continuously cooking, serving, and taking orders. People will get their food served as soon as it is cooked. - [Stack Overflow](https://stackoverflow.com/questions/748175/asynchronous-vs-synchronous-execution-what-does-it-really-mean/26804844#26804844)

Note:

* Another scenario - The boss tells me to do it, and rather than waiting right there for my work, the boss goes off and does other tasks. When I finish my job I simply report to my boss and say: "I'm DONE!" This is Asynchronous Execution.
* This is clearly also much more efficient!

---

### However

* Getting your order in before your neighbouring table does not guarantee your food will come first.

Note:
* For example your food might take longer to cook.
* Network requests are a good real-world example of this

---

### How does this work?

---

### Three main concepts

* The call stack
* The callback queue
* The event loop

---

### The call stack

* Executes code
* Can only execute one piece of code at a time (single threaded)
* Records where we are in our program
* If we step into a function, we push something onto the stack. If we return from a function, we pop off the top of the stack.

--- 

### The callback queue

* Keeps references to any callbacks that need to be processed
* Event based - callbacks are added when actions complete

---

### The Event Loop

* One simple job - to monitor the call stack and the callback queue.
* If the call stack is empty:
  * Takes the first event on the callback queue and pushes it to the call stack.
  * The call stack then executes the callback.
* Performs this repeatedly until the call stack and callback queue are empty.

Note:

* [Example](http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)
* Written by a guy named Philip Roberts who has given a great conference talk "What the heck is the event loop anyway?"

---

![Event loop](assets/image/eventloop.png)

---?image=assets/image/problem.jpg

## The problem

---

* You are organising a birthday party for your wife.
* You have a friend who is always late and you can’t rely on them to show up on time.
* **So you start the party without them!**
* But what if your friend is bringing the drinks? We might have to wait before we can start!

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

#### 3 seconds

The execution of these lines happens concurrently. The JavaScript interpretor does not wait for the timeout before moving on to executing the next line.

---

* All timers start at near enough the same time
* But what if we want to start our timers one after another?

Note:

* Usually takes around 10 microseconds to execute

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

Note:

* Can you imagine if we were making 100s of network requests using this method?

---

### Callback hell

![Callback hell](assets/image/callbackhell.png)

Note:

* Tell story about installing Doom 2 on 486.

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
};

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

* A Promise is a proxy for a value
* Essentially an object with a `.then()` method that takes a callback function
* The callback fires when the Promise's `resolve()` function fires

Note:

* Talk about catch and finally (not yet within core)

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
* Lots of boilerplate `.then()`, `.catch()` and (soon) `.finally()` code

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
};

yield timeout(1000);
console.log(1);

yield timeout(2000);
console.log(2);

yield timeout(3000);
console.log(3);
``` 

---

* Part of the ES2015 specification
* First available in Node.js v0.12.18 (behind a runtime flag)
* Can be used as an iterator function

Note:

* Mention how in the real world this wouldn't work without co() as yield has to be within a generator function (TJ Holowaychuk)
* Good use case for this is paging

---

```javascript
const example = function* () {
  yield 1;
  yield 2;
  yield 3;
};

const iterator = example();

console.log(iterator.next()); // {value: 1, done: false}
console.log(iterator.next()); // {value: 2, done: false}
console.log(iterator.next()); // {value: 3, done: false}
console.log(iterator.next()); // {value: undefined, done: true}
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

---

```javascript
const timeout = function* (ms) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve();
    }, ms);
  });
};

const timer = async () => {
  await timeout(1000);
  console.log(1);

  await timeout(2000);
  console.log(2);

  await timeout(3000);
  console.log(3);
};

timer();
```

---

* Part of the ES2017 specification
* First available in Node.js v7.6
* Supported by all major browsers **excluding** Internet Explorer Edge

---?image=assets/image/information.jpg

## More information

* [Event loop explaination](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)
* [Event loop demonstration](http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)
* [What the heck is the event loop anyway?](http://latentflip.com/what-is-the-event-loop-anyway)
* [JavaScript: The Good Parts by Douglas Crockford](https://www.amazon.co.uk/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742/ref=sr_1_1?ie=UTF8&qid=1521391685&sr=8-1&keywords=douglas+crockford)
* [Slides](https://gitpitch.com/kslat3r/async-js-old-and-new)