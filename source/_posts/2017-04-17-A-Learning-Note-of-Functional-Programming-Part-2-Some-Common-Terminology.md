---
title: A Learning Note of Functional Programming Part 2 - Some Common Terminology
date: 2017-04-17 23:28:59
tags: [meta, functional programming, js]
---

# Preface
This is another note for introducing some common terms or words that you may see in functional programming world.

Examples here will be implemented in either vanilla JS or [Ramda](http://ramdajs.com/) - a functional library that focuses on functional programming than other general-purpose same purpose libraries.

# Map
`Map` is a function which transforms data from one to any type (including itself):

```js
// array of number to array of number (transform to itself)
// [2,3,4]
[1,2,3].map(x => x + 1);

// array of number to array of obj (transform to other types)
// [
//   { num: 1 },
//   { num: 2 },
//   { num: 3 }
// ]
[1,2,3].map(x => ({ num: x }));
```

# Filter
`Filter` is a function which takes a **predicate** (a function takes an argument and return a boolean) and an list and finally returns an list:

```js
// array of number to array of number
// [2,4]
[1,2,3,4].filter(x => x % 2 === 0);
```

# Reduce
`Reduce` takes a function, an initial value and transforms data to any type (including itself):

```js
// array of number to nunber
// 10
[1,2,3,4].reduce(
  (acc, value) => {
    acc += value;
    return acc;
  }, 0
);
```

# Transduce
You might find that you can implement `map` and `filter` in `reduce`:

```js
const map = (f, arr) => {
  arr.reduce((acc, value) => {
    acc.push(f(value));
    return acc;
  }, []);
};

const filter = (predicate, arr) => {
  arr.reduce((acc, value) => {
    f(value) && acc.push(value);
    return acc;
  }, []);
}
```

Basically, `reduce` does two things at same time - **transform** and **accumulation**, but in FP, a good function only does one thing at a time.

Separating helps function composition and code reusability. We can achieve that by using `transduce` - a function takes `transducer`, `accumulator` functions and an initial value as arguments.

```js
// Ramds's transduce.
const data       = [1,2,3];
const double     = (x) => x * 2; // transformer
const add        = (x, y) => x + y; // accumulator
const transducer = R.map(double); // transformer's collection
R.transduce(transducer, add, 0, data); // 12
```

# Curry
Outside the world of FP, functions can only be carried out when all arguments have been passed in.
```js
const add = (x, y) => x + y;
add(1, 2); // 3
```

In FP world, functions will be executed only when the last argument has been passed in. A curried function with partially arguments being passed in is called **partially applied** function.

```js
// Here I use Ramda's curry to make a function curried
const add = R.curry((x, y) => x + y);
const add1 = add(1); // when the last argument hasn't been fulfilled, returns a function instead;
add1(2); // 3
add(1)(2); // or you can do this
add(1, 2); // all of them result the same
```

And here is a simple `curry` implementation taking up to 2 arguments only:

```js
const curry      = (fn) => (x) => (y) => fn(x, y);
const add        = (x, y) => x + y;
const curriedAdd = curry(add);
curriedAdd(1)(2); // 3
```

Curry is good for function composition, I will introduce them in next section.

# Compose
`compose` is a function that takes many **transformers** and compresses them into a function. This is also called `pipe` or `sequence` in other languages or libraries.

For the sake of simplicity I only make `compose` take two functions. In practical world `compose` can actually take as many functions as you want, not limit to two.

```js
const compose            = R.curry((f, g) => (val) => f(g(val)));
const trim               = (x) => x.trim();
const toUpperCase        = (x) => x.toUpperCase();
const trimAndToUpperCase = compose(trim, toUpperCase);
trimAndToUpperCase("  Hello World  "); // "HELLO WORLD"
```

Beware of its evaluation order - `f(g(val))`, functions will be evaluated in upside down. If you think it is not human friendly, in Ramda you can use [pipe](http://ramdajs.com/docs/#pipe) to get it evaluated from top to down.

Here it's why `curry` shines. The benefit of `curry` is when using with `compose`, we don't have to mention data when building our transformer. Processing data without mentioning **at initial** is called **point-of-free** or **pointfree**. In other words we only care about our data's name when we start transforming.

```js
const add1          = (x) => x + 1;
const double        = (x) => x * 2;

// not pointfree
const add1AndDouble = (data) => double(add1(data));

// pointfree
const betterAdd1AndDouble = R.compose(add1, double);
```

# Functor
`Functor` in short explanation is:
- a container traps values inside.
- container can trap anything, like function or even `Functor` itself.
- `Functor` is an **mappable** object. Something implements `map` function.

Here is a `Functor` implemented in OOP.

```js
class Functor {
  constructor(val){
    this._val = val;
  }

  static of(val){
    return new Functor(val);
  }

  map(f){
    return Functor.of(f(this._val));
  }
}

// { _val: 6 }
Functor.of(5).map(x => x + 1);
```

There is a very common example of `Functor` that every JS developer is familiar with - array.

```js
[1,2,3].map(x => x + 1);
```

There are two common advanced types of `Functor` - one is `Maybe` and the other is `Either`.

### Maybe
In other languages like Scala or OCaml, `Maybe` is also called `Option`. `Maybe` is a type which represents **something** or **nothing**.

This is especially good when dealing with `Functors` containing a **null** value, because when you try to apply function to **null** value, it fails softly.

You won't confuse your user with an wild error or stuck them in plain white screen.

```js
class Maybe extends Functor {
  static of(val){
    return new Maybe(val);
  }

  map(f){
    return Maybe.of(this._val && f(this._val));
  }
}

const add1 = (x) => x + 1;
Maybe.of(5).map(add1); // 6
Maybe.of(null).map(add1); // null
```

# Either
Most programming languages have `throw/catch` mechanics for error handling,
however `throw/catch` isn't nice in FP because of two reasons:

- it breaks the composition of functions.
- `throw/catch` hard fails program if errors were not caught. This is not a pure action.

`Either` is the right candidate for this scenario. Basically it's like **ternary operator** `condition ? exp1 : exp2`,
except it's a function:

```js
either(left, right, val)
```

It execute/return `right` only when the condition of `right` has been fulfilled, otherwise `left` instead.

Here it's a simple implementation of `Either`

```js
class Either{
  constructor(left, right){
    this.left = left;
    this.right = right;
  }

  static of(left, right){
    return new Either(left, right);
  }

  map(f){
    return this.right ? Either.of(this.left, f(this.right)) : Either.of(f(this.left), this.right);
  }
}

const add = (x) => x + 1;
Either.of(123, 456).map(add); // Either(123, 457)
Either.of(123, null).map(add); // Either(124, null)
```

# Applicative Functor
`Applicative Functor (AP)` is an advanced type of `Functor`. I just mentioned `Functor` can contain anything including functions, and one of the definition of `Functor` is it's **mappable**. Now here comes a question - how do you `map` a function?

The answer is we use `ap` to apply arguments to functions:

```js
class Functor{
  constructor(val){
    this._val = val;
  }

  static of(val){
    return new Functor(val);
  }

  map(f){
    return Functor.of(f(this._val));
  }

  // F means functor not function
  ap(F){
    return Functor.of(this._val(F._val));
  }
}

const add = curry((x, y) => x + y); // a curried version of add

Functor.of(add).ap(Functor.of(5)).ap(Functor.of(6)); // Functor(11)
Functor.of(add(5)).ap(Functor.of(6)); // Functor(11)
Functor.of(5).map(add).ap(Functor.of(6)) // Functor(11)
Functor.of(5).map(add(6)); // Functor(11)
```

### lift
Let's take a look at this example more closely:

```js
Functor.of(add).ap(Functor.of(5)).ap(Functor.of(6));
```

This syntax is not quite elegant and functional enough. What if I already have `add` function and I want to make it able to automatically process it's argument in `Functor` level?

```js
// As we know `Array` is a common kind of Functor.
// expect [3]
add([1], [2]); // "12" Oops
```

let's use `lift` to **lift its arguments to functor** without using `Functor.of(x)`


```js
// Use Ramda's add because it's already curried.
// Use Ramda's lift to do lifting.
const addA2 = lift(add);
addA2([1,2], [3,4]); // [4,7,5,8] remember functor is mappable.
```

# Monad
There is another scenario - anything can be a value of `Functor`, including `Functor` itself.

```js
Functor.of(
  Functor.of(
    Functor.of(42)
  )
);
```

With `Functor` in this status, our `map` magic doesn't work. In order to apply function to our value, we have to **flatten** it first.

```js
class Functor{
  constructor(val){
    this._val = val;
  }

  static of(val){
    return new Functor(val);
  }

  map(f){
    return Functor.of(f(this._val));
  }

  join(){
    return this._val;
  }

  flatMap(f){
    return this.join().map(f);
  }
}

const add1 = (x) => x + 1;
const num = Functor.of(Functor.of(42)).flatMap(add1); // Functor(43)
```

The `flatten` and `map` approach is called `flatMap`. `flatMap` is also called `chain` in JS or other languages.

# References
- [functional programming jargon](https://github.com/hemanth/functional-programming-jargon)
- [函数式编程入门教程](http://www.ruanyifeng.com/blog/2017/02/fp-tutorial.html)
- [Reduce 和 Transduce 的含义](http://www.ruanyifeng.com/blog/2017/03/reduce_transduce.html)
- [Understanding Transducers in JavaScript](https://medium.com/@roman01la/understanding-transducers-in-javascript-3500d3bd9624)
- [Functors, Applicatives, and Monads in Plain English](http://www.russbishop.net/monoids-monads-and-functors)
- [category theory for beginner](https://www.slideshare.net/kenbot/category-theory-for-beginners)
- [mostly adequate guide](https://drboolean.gitbooks.io/mostly-adequate-guide/content/)
- [Javascript Functor, Applicative, Monads in pictures](https://medium.com/@tzehsiang/javascript-functor-applicative-monads-in-pictures-b567c6415221)
- [Can't wrap my head around “lift” in Ramda.js](http://stackoverflow.com/questions/36558598/cant-wrap-my-head-around-lift-in-ramda-js)
