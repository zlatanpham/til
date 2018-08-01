# Why Curry Helps

A programmer’s pipe-dream is to write code, and be able to use it repeatedly with little effort. It’s _expressive_ because you write in a way that expresses what is needed, and it’s _reuse_ because.. well, you’re reusing. What more could you want?

[curry](https://npmjs.org/package/curry) can help.

### What is currying, and why is it so damn tasty? {#what-is-currying-and-why-is-it-so-damn-tasty}

Normal function invocation in JavaScript goes something like this:

```javascript
var add = function(a, b){ return a + b }
add(1, 2) //= 3
```

A function takes a number of arguments, and returns a value. I can call it with too few \(with odd results\), or too many \(where the excess normally get ignored\):

```javascript
add(1, 2, 'IGNORE ME') //= 3
add(1) //= NaN
```

A _curried_ function is one where multiple arguments are described by a series of one-argument functions. For example, curried addition would be:

```javascript
var curry = require('curry')
var add = curry(function(a, b){ return a + b })
var add100 = add(100)
add100(1) //= 101
```

A curried function accepting multiple arguments would be written as:

```javascript
var sum3 = curry(function(a, b, c){ return a + b + c })
sum3(1)(2)(3) //= 6
```

Since this is ugly in JavaScript’s syntax, [curry](https://npmjs.org/package/curry) allows you can also call a function with multiple arguments at once:

```javascript
var sum3 = curry(function(a, b, c){ return a + b + c })
sum3(1, 2, 3) //= 6
sum3(1)(2, 3) //= 6
sum3(1, 2)(3) //= 6
```

### So .. what? {#so--what}

If you’re not used to languages in which curried functions are part of daily life \([Haskell](http://learnyouahaskell.com/) comes to mind\), it’s probably not obvious what advantage this gives us. To my mind, the two main ones are:

* Little pieces can be configured and reused with ease, without clutter;
* Functions are used throughout.

#### The little pieces {#the-little-pieces}

Let’s take an obvious example; mapping over a collection to get the ids of its members:

```javascript
var objects = [{ id: 1 }, { id: 2 }, { id: 3 }]
objects.map(function(o){ return o.id })
```

If you’re looking for the REAL logic in that second line, let me pick it out for you:

> MAP over OBJECTS to get IDS

There’s a lot of implementation cruft in just that line; in the form of the function definition. Let’s clean that up:

```javascript
var get = curry(function(property, object){ return object[property] })
objects.map(get('id')) //= [1, 2, 3]
```

Now we’re talking in terms of the real logic of the operation - map over these objects, getting their ids. BAM. What we’ve really created in the _get_ function is a **function that can be partially configured**.

What if we want to re-use the ‘get ids from a list of objects’ functionality, though? Let’s do it the naive way:

```javascript
var getIDs = function(objects){
    return objects.map(get('id'))
}
getIDs(objects) //= [1, 2, 3]
```

Hrm, we seem to be back to cluttery from elegant and terse. What can we do about this? Ah - what if map could be partially configured with a function, but no collection yet?

```javascript
var map = curry(function(fn, value){ return value.map(fn) })
var getIDs = map(get('id'))

getIDs(objects) //= [1, 2, 3]
```

We’re starting to see that, if our basic building blocks are curried functions, we can easily create fresh, new functionality from them. Even more excitingly, the code reads like the logic of the domain you’re working in.

#### Functions through and through {#functions-through-and-through}

Another advantage of this approach is that it encourages the creation of functions; rather than of methods. While methods are beautiful things - allowing polymorphism, and very readable code - they aren’t always the tool for the job, such as in heavily async code.

In this toy example, let’s ‘get’ some data from a server and process it in some useful way. The data looks something like this:

```javascript
{
    "user": "hughfdjackson",
    "posts": [
        { "title": "why curry?", "contents": "..." },
        { "title": "prototypes: the short(est possible) story", "contents": "..." }
    ]
}
```

Your task is to get the the titles for each of this users posts. Gogogo!

```javascript
fetchFromServer()
    .then(JSON.parse)
    .then(function(data){ return data.posts })
    .then(function(posts){
        return posts.map(function(post){ return post.title })
    })
```

Okay, that wasn’t fair, I rushed you. \(Also, I wrote that code on your behalf - perhaps you would solve it more elegantly, but I digress\).

Since chains of promises \(or, if you prefer, callbacks\) fundamentally _work_ with functions, you can’t easily just map over an value when one is available from the server without first wrapping it up in all that visual \(and mental\) clutter.

Let’s go again, this time using the tools we’ve already defined:

```javascript
fetchFromServer()
    .then(JSON.parse)
    .then(get('posts'))
    .then(map(get('title')))
```

This is lean logic, easily expressed; something we couldn’t achieve nearly so easily without curried functions.

## tl;dr {#tldr}

[curry](https://npmjs.org/package/curry) gives you a tantalising amount of expressive power.

I suggest you pick it up, and play around with it. If you’re familiar with the concept already, I suspect you’ll find the API straight forward and satisfying. If not, you owe it to yourself and your colleagues to consider it.

