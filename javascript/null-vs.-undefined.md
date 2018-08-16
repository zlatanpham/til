---
description: >-
  Learn the differences and similarities between null and undefined in
  JavaScript
---

# Null vs. Undefined

At first glance, `null` and `undefined` may seem the same, but they are far from it. This article will explore the differences and similarities between `null`and `undefined` in JavaScript.

**What is null?**

There are two features of `null` you should understand:

* `null` is an empty or non-existent value.
* `null` must be assigned.

Here’s an example. We assign the value of `null` to `a`:

```javascript
let a = null;
console.log(a);
// null
```

**What is undefined?**

Undefined most typically means a variable has been declared, but not defined. For example:

```javascript
let b;
console.log(b);
// undefined
```

You can also explicitly set a variable to equal undefined:

```javascript
let c = undefined;
console.log(c);
// undefined
```

Finally, when looking up non-existent properties in an object, you will receive undefined:

```javascript
var d = {};
console.log(d.fake);
// undefined
```

**Similarities between null and undefined**

In JavaScript there are only **six falsy values.** Both `null` and `undefined` are two of the six falsy values. Here’s a full list:

* false
* 0 \(zero\)
* “” \(empty string\)
* **null**
* **undefined**
* NaN \(Not A Number\)

Any other value in JavaScript is considered truthy.

> If you’re not familiar with truthy/falsy values in JavaScript, I recommend reading my previous article: [**JavaScript — Double Equals vs. Triple Equals**](https://codeburst.io/javascript-double-equals-vs-triple-equals-61d4ce5a121a)

Also in JavaScript, there are six primitive values. Both `null` and `undefined`are primitive values. Here is a full list:

* Boolean
* **Null**
* **Undefined**
* Number
* String
* Symbol

All other values in JavaScript are objects \(objects, functions, arrays, etc.\).

Interestingly enough, when using `typeof` to test `null`, it returns `object`:

```javascript
let a = null;
let b;

console.log(typeof a);
// object

console.log(typeof b);
// undefined
```

This has occurred since the beginning of JavaScript and is generally regarded as a mistake in the original JavaScript implementation.

> If you’re not familiar with data types in JavaScript, I recommend reading my previous article: [**JavaScript Data Types Explained**](https://codeburst.io/javascript-data-types-explained-347555cd2d4d)

**null !== undefined**

As you can see so far, `null` and `undefined` are different, but share some similarities. Thus, it makes sense that `null` does not strictly equal `undefined`.

```javascript
null !== undefined 
```

But, and this may surprise you, `null` loosely equals `undefined`.

```javascript
null == undefined
```

In JavaScript, a double equals tests for **loose equality** and preforms _type coercion_. This means we compare two values after converting them to a common type.

**Practical Differences**

All of this is great, but what about a practical difference between `null` and `undefined`?

Consider the following code snippet:

```javascript
let logHi = (str = 'hi') => {
  console.log(str);
}
```

The code above creates a function named `logHi`. This function requires one parameter and sets the default of that parameter to `hi` if it isn’t supplied. Here’s what that looks like:

```javascript
logHi();
// hi
```

We can also supply a parameter to overwrite this default:

```javascript
logHi('bye');
// bye
```

With default parameters, `undefined` will use the default while `null` does not.

```javascript
logHi(undefined);
// hi

logHi(null);
// null
```

> Thanks to [Tim Branyen](https://medium.com/@tbranyen) for the code inspiration.

**Summary**

* `null` is an assigned value. It means _nothing._
* `undefined` typically means a variable has been declared but not defined yet.
* `null` and `undefined` are falsy values.
* `null` and `undefined` are both primitives. However an error shows that `typeof null = object`.
* `null !== undefined` but `null == undefined`.

