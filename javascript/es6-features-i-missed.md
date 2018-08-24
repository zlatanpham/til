# ES6 features I missed

Object destructuring allows is assigning a value to multiple variables.

```javascript
let { x: first, x: second } = { x: 4 };
console.log( first, second ); // 4, 4
```

Passing in a set of variables to a function from an array.

```javascript
function foo(a, b, c) { console.log(`a=${a}, b=${b}, c=${c}`)} 
let data = [5, 15, 2];
foo( ...data); // a=5, b=15, c=2
```



