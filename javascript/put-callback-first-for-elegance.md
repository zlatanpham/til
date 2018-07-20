---
description: >-
  Combining functions into custom iterator callbacks and using _ will be easier
  if you put callback function first and data second.
---

# Put callback first for elegance

JavaScript allows to combine small functions into more complex ones. Each input small function can be very simple to test and understand, while easy composition allows to naturally express the desired sequence of actions.

Take a look at Reginald Braithwaite's slides [JavaScript Combinators](https://speakerdeck.com/raganwald/javascript-combinators) or the presentation [video](http://vimeo.com/97408202). Notice one curious detail: instead of using `map(data, callback)` he switched to `map(callback, data)`. Why?

Let us code a simple example to understand why putting callback first leads to simpler and more elegant code. Start with an array of numbers and select only the even ones:

```javascript
var numbers = [1, 2, 3, 4, 5];
var isEven = function (x) { return x % 2 === 0; }
console.log(numbers.filter(isEven));
// [2, 4]
```

Right now we are using JavaScript's prototypical inheritance. Every array inherits from `Array.prototype`, in particular we use method `filter`. Because we want to make a reusable filter call, we would like to avoid relying on the prototype. At first we can use the prototype's method directly.

```javascript
var filter = Array.prototype.filter;
console.log(filter.call(numbers, isEven));
// [2, 4]
```

Nice, but `filter.call` is an immediate call. It cannot be passed around, or composed with other functions. So let us write separate `filter` function

```javascript
var filter = function (data, cb) {
  return data.filter(cb);
};
console.log(filter(numbers, isEven));
// [2, 4]
```

Our function `filter` takes data as first argument and callback to run as second, following the approach taken by [underscore.filter](http://underscorejs.org/#filter) and [lodash.filter](http://lodash.com/docs#filter).

### [Functional combinators \#](https://glebbahmutov.com/blog/put-callback-first-for-elegance/#functional-combinators)

There are two ways in JavaScript to combine functions into new ones: composition and argument binding. Let us start with composition.

Instead of writing `isOdd` function from scratch, we can compose the existing `isEven` function with a new `not` adapter function:

```javascript
var isEven = function (x) { return x % 2 === 0; }
var not = function (fn) {
  return function () {
    return !fn.apply(null, arguments);
  };
};
var isOdd = not(isEven); // composition
console.log(filter(numbers, isOdd));
// [1, 3, 5]
```

This composition is very simple and elegant: `not(isEven)` is a new function, and its meaning is clear from naturally reading the expression.

Functional binding with left to right argument binding is included in EcmaScript5. We can create new functions using `.bind` syntax.

```javascript
function larger(limit, x) {
  return x > limit;
}
var larger3 = larger.bind(null, 3);
console.log(larger3(10)); // true
console.log(filter(numbers, larger3));
// [4, 5]
```

Here is the key observation about binding arguments and function reuse.

> Place the arguments LESS likely to change first \(on the left\).

Take `larger` function above for example. We are likely to reuse this function with single limit and multiple values of `x`. Thus by placing _limit_ first, we are allowing easier future reuse

```javascript
var larger3 = larger.bind(null, 3);
larger3(10); // true
larger3(1);  // false
...
```

### [Reusing filter via binding \#](https://glebbahmutov.com/blog/put-callback-first-for-elegance/#reusing-filter-via-binding)

Let us go back to the function `filter` and how we can reuse it by binding arguments. What could be a common use case for binding? I see binding the callback function!

```javascript
function filter(data, cb) { ... }
var filterEven = filter.bind(null, isEven) // ?
// DOES NOT WORK
```

This approach does not work because we put `data` as the first argument, and callback second. Ok, let us write another functional adapter and bind arguments _from the right_. Except this approach runs into troubles, because both _underscore_ and _lodash_ sandwich the callback between arguments

```javascript
function filter(data, cb, context) ...
```

The next version of lodash 2.5.0 will have selective argument binding, which allows to bind just some arguments, leaving others free

```javascript
var _ = require('lodash');
var filterEven = _.partial(filter, _, isEven);
console.log(filterEven(numbers));
// [2, 4]
```

```javascript
function filter(cb, data) { ... }
var filterEven = filter.bind(null, isEven);
console.log(filterEven(numbers));
// [2, 4]
```

I prefer not to rely on 3rd party libraries when the existing tools work nicely, if only I planned ahead. So I place the callback function first, and _data_ second.

```javascript
function filter(cb, data) { ... }
var filterEven = filter.bind(null, isEven);
console.log(filterEven(numbers));
// [2, 4]
```

Again, the JavaScript code is less likely to be dynamic than the data, so place data last.

### [Making \_ more functional \#](https://glebbahmutov.com/blog/put-callback-first-for-elegance/#making-_-more-functional)

If placing callback first makes the function easier to reuse, can we "fix" the existing methods in underscore or lodash libraries? Yes, by providing an easy way to reorder first two arguments \(usually data and callback\), and by providing a shortcut to reorder and bind first argument. I wrote a small utility [scoreunder](https://github.com/bahmutov/scoreunder) with just 2 functions

```javascript
// simple argument flip
var under = require('scoreunder');
var filter = under.ba(_.filter);
console.log(filter(isEven, numbers));
// [2, 4]
```

There is a tiny utility _flip2_ with same purpose in [lodash-contrib](https://github.com/Empeeric/lodash-contrib) library.

Second, the _scoreunder_ provides a combined "flip and bind first argument" function

```javascript
var under = require('scoreunder');
var filterEven = under.partialFn(_.filter, isEven);
console.log(filterEven(numbers));
// [2, 4]
```

Utility `partialFn` makes underscore / lodash a pleasure to use.

### [Final thoughts \#](https://glebbahmutov.com/blog/put-callback-first-for-elegance/#final-thoughts)

Thanks to [John-David Dalton](https://github.com/jdalton) for [pointing out](https://github.com/bahmutov/scoreunder/commit/3ded6449dc0bc45c2e3d9bd2b8d2c354180a5cb6) the new things coming to lodash's API.

There is an interesting presentation you can watch: [Hey Underscore, you are doing it wrong!](https://www.youtube.com/watch?v=m3svKOdZijA) discussing why callback should be first argument.

Other functional libraries, like [ramda](https://github.com/CrossEye/ramda) and [wu](http://fitzgen.github.io/wu.js/) place callback first.

These two great books discuss JavaScript functional programming in detail, showing all sorts of interesting combinators and adapters. I highly recommend reading them in chunks, since each part needs time to digest.

* [JavaScript Allonge](https://leanpub.com/javascript-allonge)
* [Functional JavaScript](http://www.amazon.com/gp/product/1449360726) 

Source: [https://glebbahmutov.com/blog/put-callback-first-for-elegance/](https://glebbahmutov.com/blog/put-callback-first-for-elegance/)

