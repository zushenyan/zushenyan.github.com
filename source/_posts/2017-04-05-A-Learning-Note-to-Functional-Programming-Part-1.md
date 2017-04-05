---
title: A Learning Note to Functional Programming - Part 1
date: 2017-04-05 22:08:57
tags: [meta, functional programming, js]
---

# What makes FP great?
Functional Programming (FP) has been a trendy word for a while, but I wonder what makes people think FP is good? Here is my conclusion:

## The need for concurrent/parallelism
Since we got more than one core on CPU, we started thinking on how to process tasks more efficiently.

To achieve that we have to adapt the power of concurrent/parallelism, however the biggest problem of multithreading is **how do we make sure data shared between threads is safe**.

In imperative programming (or languages that are not encourage immutable), things are **mutable**, a variable can be modified by reference without creating a new one, but when it comes to multithreading, with mutable variables, developers will have to use `locks` to manage access permission between threads, but this technique doesn't solve this problem nicely, because the natural of variables are still mutable, once it causes bugs, it will cost developers lots of time to spot and solve issues.

Another point that makes FP shining is **declarative**. Declarative means telling computer what to do, and imperative, on the contrary, means arranging every command manually, step by step. The benefit of declarative is yielding the rights to rearrange commands for the compiler. This approach saves a lot of line of code, making your code more cleaner and easier to read.

# Features of FP

## First-class Function
It means you can pass functions as parameters or return functions as an result.

```js
// take function as a parameter
const foo = (cb) => {
  console.log("hello world");
  cb();
};

// return function as an result
const bar = (name) => {
  return () => {
    console.log(`Hello ${name}`);
  };
};
```

## Pure Functions
Pure function means - every time you pass the same argument, you should get the same result. If a function violates the rule, then the function is **impure**. Things that can lead to impure are like depending on external result or IO.

#### depending on external result
```js
let a = 5;

const add5 = (num) => num + a;

a = 10;
```

#### IO
```js
// promise will return either result or failure.
const fetchData = () => fetch("http://some-awesome-service/api");

fetchData()
  .then(result => console.log(result))
  .catch(err => console.log(err));
```

## Referential Transparency
You can replace an expression with it's result without changing the meaning of program.

```js
const add5 = (num) => num + 5;
const add10 = (num) => add5(add5(num));

// or replace with
const add10 = (num) => num + 10;
```

## Lazy Evaluation
There is another term called **strict(eager) vs non-strict(lazy) evaluation**. Lazy evaluation means computing the result only when it's needed. Let's take `filter` for example, if we want to filter out the first 3 of the even numbers from a list of numbers, strict evaluation traverses all the elements in the list and return first 3 even numbers, but lazy evaluation will traverse until it meets the third odd number.

This approach might greatly improve performance because it doesn't traverse all the elements, but the down side is making evaluation on time complexity harder.

```js
// because JavaScript doesn't have lazy evaluation built-in, here I use lodash to help demonstration.

// lodash will only perform lazy evaluation when elements length is larger than 200.
const arr = _.range(200);

let counter1 = 0;
let counter2 = 0;

// strict evaluation
arr.filter(ele => {
    counter1++;
    return ele % 2 === 0;
  })
  .slice(3);

// lazy evaluation
_(arr).filter(ele => {
    counter2++;
    return ele % 2 === 0;
  })
  .take(3)
  .value();

console.log(counter1); // 200
console.log(counter2); // 5
```

## Declarative vs Imperative
Declarative tells compiler what to do instead of telling commands step by step. By using declarative we hide the working details, which gives compiler the ability to rearrange the execution steps of commands.

```js
const arr = [1,2,3,4,5];

// declarative
arr.map(ele => ele + 1);

// imperative (and mutable)
for(let i = 0; i < arr.length; i++){
  arr[i] += 1;
}
```

# References
- [Functional Programming on Wikipedia](https://en.wikipedia.org/wiki/Functional_programming)
- [Master the JavaScript Interview: What is Functional Programming?](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-functional-programming-7f218c68b3a0)
- [Seven Concurrency Models Weeks Programmers](https://www.amazon.com/Seven-Concurrency-Models-Weeks-Programmers/dp/1937785653)
