# Favoring Curry

My [recent article](http://fr.umio.us/why-ramda/) on functional composition in [Ramda](https://github.com/ramda/ramda) breezed over an important topic. In order to do the sort of composition we would like with Ramda functions, we need these functions to be curried.

Curry? Like the spicy food? What? Where?

Actually, `curry` is named for Haskell Curry, who was one of the first to investigate such techniques. \(Yes, they used his first name for a functional programming language too; not only that, but Curry's middle initial was 'B', which of course stands for [Brainf\*ck](http://en.wikipedia.org/wiki/Brainfuck).\)

Currying is the process of turning a function that expects multiple parameters into one that, when supplied fewer parameters, returns a new function that awaits the remaining ones.

The basics look like this. Here's a plain function:

```javascript
// uncurried version
var formatName1 = function(first, middle, last) {
    return first + ' ' + middle + ' ' + last;
};
formatName1('John', 'Paul', 'Jones');
//=> 'John Paul Jones' // (Ah, but the musician or the admiral?)
formatName1('John', 'Paul');
//=> 'John Paul undefined');
```

But a curried version behaves more usefully:

```javascript
// curried version
var formatNames2 = R.curry(function(first, middle, last) {
    return first + ' ' + middle + ' ' + last;
});
formatNames2('John', 'Paul', 'Jones');
//=> 'John Paul Jones' // (definitely the musician!)
var jp = formatNames2('John', 'Paul'); //=> returns a function
jp('Jones'); //=> 'John Paul Jones' (maybe this one's the admiral)
jp('Stevens'); //=> 'John Paul Stevens' (the Supreme Court Justice)
jp('Pontiff'); //=> 'John Paul Pontiff' (ok, so I cheated.)
jp('Ziller'); //=> 'John Paul Ziller' (magician, a wee bit fictional)
jp('Georgeandringo'); //=> 'John Paul Georgeandringo' (rockers)
```

or

```javascript
['Jones', 'Stevens', 'Ziller'].map(jp);
//=> ['John Paul Jones', 'John Paul Stevens', 'John Paul Ziller']
```

And you can do this in multiple passes, as well:

```javascript
var james = formatNames2('James'); //=> returns a function
james('Byron', 'Dean'); //=> 'James Byron Dean' (rebel)
var je = james('Earl'); also returns a function
je('Carter'); //=> 'James Earl Carter' (president)
je('Jones'); //=> 'James Earl Jones' (actor, Vader)
```

\(Some will insist that what we're doing is more properly called "partial application", and that "currying" should be reserved for the cases where the resulting functions take one parameter, each resolving to a separate new function until all the required parameters have been supplied. They can please feel free to keep on insisting.\)

### Booooooring! What Can You Do For ME? <a id="booooooringwhatcanyoudoforme"></a>

Here is a slightly more meaningful example. If you want to find the sum of a collection of numbers, you could do this:

```javascript
// Plain JS:
var add = function(a, b) {return a + b;};
var numbers = [1, 2, 3, 4, 5];
var sum = numbers.reduce(add, 0); //=> 15
```

And if you wanted to write a generic function that would total any list of numbers, you might write:

```javascript
var total = function(list) {
    return list.reduce(add, 0);
};
var sum = total(numbers); //=> 15
```

In Ramda, `total` and `sum` are very similar. You can define `sum` like this:

```javascript
var sum = R.reduce(add, 0, numbers); //=> 15
```

But because `reduce` is a curried function, when you skip the last parameter, as in the definition of `total`:

```javascript
// In Ramda:var total = R.reduce(add, 0);  // returns a function
```

you simply get back a function you can call:

```javascript
var sum = total(numbers); //=> 15
```

Note again just how similar the definition of the function and the application of that function to data can be:

```javascript
var total = R.reduce(add, 0); //=> function:: [Number] -> Number
var sum =   R.reduce(add, 0, numbers); //=> 15
```

### Don't Care: I'm Not a Math Geek! <a id="dontcareimnotamathgeek"></a>

So you do web development, huh? You make AJAX calls to the server? You're using [Promises](http://promises-aplus.github.io/promises-spec/), I hope? Do you have to manipulate the data that comes back, filter it, subset it? Or you do server-side development? You asynchronously query a no-SQL database, and manipulate those results?

The best thing I can suggest is that you go look at Hugh FD Jackson's excellent post, [Why Curry Helps](http://hughfdjackson.com/javascript/why-curry-helps/). It's the best reading I've seen on this. And if you're a visual learner, spend half an hour on Dr. Boolean's video [Hey Underscore, You're Doing It Wrong](http://www.youtube.com/watch?v=m3svKOdZijA). \(And don't worry about the title; he doesn't spend _too much_ time actually trashing libraries in there.\)

Really go ahead. Look at those; they can explain better than I can; you already can see that I'm verbose, windy, wordy and downright garrulous. And if you've seen those, you can skip the rest of this section. They've already said it better than I can.

You've been warned.

Suppose we expect to get some data like this:

```javascript
var data = {
    result: "SUCCESS",
    interfaceVersion: "1.0.3",
    requested: "10/17/2013 15:31:20",
    lastUpdated: "10/16/2013 10:52:39",
    tasks: [
        {id: 104, complete: false,            priority: "high",
                  dueDate: "2013-11-29",      username: "Scott",
                  title: "Do something",      created: "9/22/2013"},
        {id: 105, complete: false,            priority: "medium",
                  dueDate: "2013-11-22",      username: "Lena",
                  title: "Do something else", created: "9/22/2013"},
        {id: 107, complete: true,             priority: "high",
                  dueDate: "2013-11-22",      username: "Mike",
                  title: "Fix the foo",       created: "9/22/2013"},
        {id: 108, complete: false,            priority: "low",
                  dueDate: "2013-11-15",      username: "Punam",
                  title: "Adjust the bar",    created: "9/25/2013"},
        {id: 110, complete: false,            priority: "medium",
                  dueDate: "2013-11-15",      username: "Scott",
                  title: "Rename everything", created: "10/2/2013"},
        {id: 112, complete: true,             priority: "high",
                  dueDate: "2013-11-27",      username: "Lena",
                  title: "Alter all quuxes",  created: "10/5/2013"}
        // , ...
    ]
};
```

And we need a function `getIncompleteTaskSummaries` that accepts a membername parameter, then fetches the data from the server \(or somewhere\), chooses the tasks for that member that are not complete, returns their ids, priorities, titles, and due dates, sorted by due date. Actually, it returns a Promise that should resolve into that sort of list.

If you pass "Scott" to `getIncompleteTaskSummaries`, it might return

```javascript
[
    {id: 110, title: "Rename everything", 
        dueDate: "2013-11-15", priority: "medium"},
    {id: 104, title: "Do something", 
        dueDate: "2013-11-29", priority: "high"}
]
```

Ok, here we go. Does code like this look at all familiar?

```javascript
getIncompleteTaskSummaries = function(membername) {
    return fetchData()
        .then(function(data) {
            return data.tasks;
        })
        .then(function(tasks) {
            var results = [];
            for (var i = 0, len = tasks.length; i < len; i++) {
                if (tasks[i].username == membername) {
                    results.push(tasks[i]);
                }
            }
            return results;
        })
        .then(function(tasks) {
            var results = [];
            for (var i = 0, len = tasks.length; i < len; i++) {
                if (!tasks[i].complete) {
                    results.push(tasks[i]);
                }
            }
            return results;
        })
        .then(function(tasks) {
            var results = [], task;
            for (var i = 0, len = tasks.length; i < len; i++) {
                task = tasks[i];
                results.push({
                    id: task.id,
                    dueDate: task.dueDate,
                    title: task.title,
                    priority: task.priority
                })
            }
            return results;
        })
        .then(function(tasks) {
            tasks.sort(function(first, second) {
                var a = first.dueDate, b = second.dueDate;
                return a < b ? -1 : a > b ? 1 : 0;
            });
            return tasks;
        });
};
```

Now wouldn't it be nicer if the code looked more like this?:

```javascript
var getIncompleteTaskSummaries = function(membername) {
    return fetchData()
        .then(R.get('tasks'))
        .then(R.filter(R.propEq('username', membername)))
        .then(R.reject(R.propEq('complete', true)))
        .then(R.map(R.pick(['id', 'dueDate', 'title', 'priority'])))
        .then(R.sortBy(R.get('dueDate')));
};
```

If so, then currying could well be for you. All the Ramda functions mentioned in this block are curried. \(In fact, pretty well all Ramda functions of more than one parameter are curried, with only a few necessary exceptions.\) In every case the currying is part of what makes it so easy to compose them into such a nice, elegant block.

Let's take a look at what's happening.

`get` \(also known as `prop`\) is defined like this:

```javascript
 ramda.get = curry(function(name, obj) {
     return obj[name];
 });
```

But when we call it above, we supply only the first parameter, `name`. As we discussed, this means that we will get back a function that is waiting for the `obj` parameter to be passed by the first `then`. That means that this:

```javascript
.then(R.get('tasks'))
```

can be thought of as simple shorthand for

```javascript
.then(R.get('tasks'))
```

Next is `propEq`, which is defined as:

```javascript
.then(function(data) {
    return data.tasks;
})
```

So when we call it with parameters `"username"` and `membername` \(the latter was supplied to our function as a parameter\), the currying gives us back a new function, something equivalent to

```javascript
ramda.propEq = curry(function(name, val, obj) {
    return obj[name] === val;
});
```

where the value of `membername` is bound to the value that was passed to us.

This function is then passed into `filter`.

Ramda's filter works much like the native filter on `Array.prototype`, but the signature is

```javascript
ramda.filter = curry(function(predicate, list) { /* ... */ });
```

So we're curried yet again, passing in only the predicate, and not the list of tasks passed through from the previous step. \(I did tell you that everything was curried, did I not?\)

We do the same sort of thing with `propEq('complete', true) -> reject` as we did with `propEq('username', membername) -> filter`. `Reject` is the same as `filter` except that it reverses the sense of it. It keeps only those ones for which the predicate returns `false.`

Ok, are you still here reading? My index fingers are getting tired. \(Really have to learn to touch-type!\) You don't really need me to explain those last two lines, right? Really? You're sure? All right! All right! Yes! ... Yes, I said I would!

So next we see

```javascript
R.pick(['id', 'dueDate', 'title', 'priority'])
```

`pick` accepts a list of property names and an object, and returns a new object with those properties copied from the original. But lo and behold, we're curried again. And since we only pass the list of property names, we get a function that will return such a new object once we supply it an object. That function gets passed into `R.map`. As with `filter`, this works much like the native Array prototype version, but with the signature:

```javascript
ramda.map = curry(function(fn, list) { /* ... */ });
```

And the broken record here will report _yet again_ -- I told you I'd be tedious -- that this function is curried, since we only supply the function from the \(curried!\) output of `pick` to this, and not the list. `then` will invoke this with the list of tasks.

OK, remember sitting in school, waiting for the for the class to end? The minute hand on the clock was stuck, and the second hand was moving through molasses? The teacher was droning on and on about the same thing over and over. Remember that? And then there was that moment, maybe two minutes before the end of the period, when the end was suddenly in sight: _Hallelujah_! I think we're there with this example. There is only this left:

```javascript
.then(R.sortBy(R.get('dueDate')));
```

We already talked about `get`. Curried like this, it returns a function that, given an object, returns its `dueDate` property. We pass this into `sortBy`, which takes a function such as this and a list and sorts the list based on the values returned by the function against the elements of the list. But wait, we don't have a list, right? Of course not. We're curried again. But when we're invoked by `.then()`, it will receive the list, passing each object to `get`, and sorting based on the results.

### So How Important Is The Currying? <a id="sohowimportantisthecurrying"></a>

This example is demonstrating the Ramda utility functions alongside the currying aspects of Ramda. Perhaps the currying is not really that important. Let's try to rewrite that without the currying:

```javascript
var getIncompleteTaskSummaries = function(membername) {
    return fetchData()
        .then(function(data) {
            return R.get('tasks', data)
        })
        .then(function(tasks) {
            return R.filter(function(task) {
                return R.propEq('username', membername, task)
            }, tasks)
         })
        .then(function(tasks) {
            return R.reject(function(task) {
                return R.propEq('complete', true, task);
            }, tasks)
        })
        .then(function(tasks) {
            return R.map(function(task) {
                return R.pick(['id', 'dueDate', 'title', 'priority'], task);
            }, tasks);
        })
        .then(function(abbreviatedTasks) {
            return R.sortBy(function(abbrTask) {
                return R.get('dueDate', abbrTask);
            }, abbreviatedTasks);
        });
};
```

That, I think, is the equivalent. It's still better than the original code. Ramda's utility functions have some -- er, utility -- even in the absence of currying. But I don't think it's even close to as readable as this:

```javascript
var getIncompleteTaskSummaries = function(membername) {
    return fetchData()
        .then(R.get('tasks'))
        .then(R.filter(R.propEq('username', membername)))
        .then(R.reject(R.propEq('complete', true)))
        .then(R.map(R.pick(['id', 'dueDate', 'title', 'priority'])))
        .then(R.sortBy(R.get('dueDate')));
};
```

And that is why we curry.

Here endeth the lesson.

I did warn you.

Next time, when I tell you to read someone else's stuff instead of mine, you'll pay attention, right? It might be too late to look at these _instead_ of reading mine, but they still are very well done, and maybe you can do it as well:

* [Why Curry Helps](http://hughfdjackson.com/javascript/why-curry-helps/), Hugh FD Jackson
* [Hey Underscore, You're Doing It Wrong](http://www.youtube.com/watch?v=m3svKOdZijA), Dr. Boolean, aka Brian Lonsdorf

There's one other that's brand-new. I just saw it today. We'll see if it stands the test of time, but for now it's a good read:

* [Put callback first for elegance](https://glebbahmutov.com/blog/put-callback-first-for-elegance/), Gleb Bahmutov

### A Dirty Little Secret <a id="adirtylittlesecret"></a>

Currying, as powerful as it is, is not enough to make your code elegant.

There seem to be three important components.

* [Last time](http://fr.umio.us/why-ramda/) I discussed **functional composition**. That is necessary for bringing together all your beautiful ideas without a lot of ugly glue code to hold them together.
* **Currying** is useful both because it's needed to support composition and because it removes tremendous amounts of boilerplate as we see above.
* A collection of **utility functions** operating on useful data structures, such as lists of objects.

One of the goals of [Ramda](https://github.com/ramda/ramda) is to provide all these in a convenient package.

