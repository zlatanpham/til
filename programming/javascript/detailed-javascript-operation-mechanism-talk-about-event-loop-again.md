# Detailed JavaScript operation mechanism: talk about Event Loop again

### First, why is JavaScript single threaded?

A big feature of the JavaScript language is single-threading, which means that only one thing can be done at the same time. So why can't JavaScript have multiple threads? This will improve efficiency.

The single thread of JavaScript is related to its purpose. As a browser scripting language, the main purpose of JavaScript is to interact with the user and manipulate the DOM. This determines that it can only be single-threaded, otherwise it will bring a very complicated synchronization problem. For example, suppose JavaScript has two threads at the same time. One thread adds content to one DOM node, and the other thread deletes the node. Which thread should the browser prevail?

So, in order to avoid complexity, JavaScript was single-threaded since its birth, which has become a core feature of the language and will not change in the future.

To take advantage of the computing power of multicore CPUs, HTML5 proposes a Web Worker standard that allows JavaScript scripts to create multiple threads, but child threads are completely controlled by the main thread and must not manipulate the DOM. Therefore, this new standard does not change the nature of JavaScript single threading.

### Second, the task queue

Single-threading means that all tasks need to be queued, and the previous task ends before the next task is executed. If the previous task took a long time, the latter task had to wait.

If the queuing is because of the large amount of calculation, the CPU is too busy, but it is not enough, but often the CPU is idle, because the IO device \(input and output device\) is very slow \(such as Ajax operation to read data from the network\), have to Wait until the results come out and go down.

The designers of the JavaScript language realized that the main thread can completely ignore the IO device, suspend the task that is waiting, and run the task that is behind. Wait until the IO device returns the result, then go back and continue the pending task.

Thus, all tasks can be divided into two types, one is synchronous and the other is asynchronous. A synchronous task refers to a task queued for execution on the main thread. Only the previous task is executed before the next task can be executed. The asynchronous task refers to entering the task queue without entering the main thread. Tasks, only the "task queue" informs the main thread that an asynchronous task can be executed before the task enters the main thread.

Specifically, the asynchronous execution mechanism is as follows. \(The same is true for synchronous execution because it can be considered as asynchronous execution without asynchronous tasks.\)

> \(1\) All synchronization tasks are performed on the main thread to form a [execution stack](http://www.ruanyifeng.com/blog/2013/11/stack.html) \(execution context stack\).
>
> \(2\) In addition to the main thread, there is also a "task queue". As long as the asynchronous task has a running result, an event is placed in the "task queue".
>
> \(3\) Once all the synchronization tasks in the "execution stack" have been executed, the system will read the "task queue" to see what events are there. Those corresponding asynchronous tasks, then end the wait state, enter the execution stack, and begin execution.
>
> \(4\) The main thread continuously repeats the third step above.

The following figure is a schematic diagram of the main thread and the task queue.

![Task queue](http://www.ruanyifeng.com/blogimg/asset/2014/bg2014100801.jpg)

As long as the main thread is empty, it will read the "task queue", which is the operating mechanism of JavaScript. This process will be repeated.

### Third, the event and callback function

The "task queue" is a queue of events \(which can also be understood as a queue of messages\). When an IO device completes a task, it adds an event to the "task queue", indicating that the associated asynchronous task can enter the "execution stack". The main thread reads the "task queue", which is to read what events are inside.

Events in the "Task Queue", in addition to events from IO devices, include user-generated events \(such as mouse clicks, page scrolls, etc.\). As long as the callback function is specified, these events will enter the "task queue" and wait for the main thread to read.

The so-called "callback" is the code that will be hung up by the main thread. Asynchronous tasks must specify a callback function. When the main thread starts executing an asynchronous task, it executes the corresponding callback function.

The "task queue" is a first-in, first-out data structure, with the previous events, which are preferentially read by the main thread. The main thread's reading process is basically automatic. As soon as the execution stack is emptied, the first event on the "task queue" automatically enters the main thread. However, due to the "timer" function mentioned later, the main thread first checks the execution time, and some events can only return to the main thread until the specified time.

### Fourth, Event Loop

The main thread reads events from the "task queue". This process is cyclic, so the entire running mechanism is called Event Loop.

To better understand the Event Loop, see the image below \(referred to from Philip Roberts's ["Help, I'm stuck in an event-loop"](http://vimeo.com/96425312)\).

![Event Loop](http://www.ruanyifeng.com/blogimg/asset/2014/bg2014100802.png)

In the above figure, when the main thread is running, a heap and a stack are generated. The code in the stack calls various external APIs, and they add various events \(click, load, done\) in the "task queue". As soon as the code in the stack is executed, the main thread will read the "task queue" and execute the callback functions corresponding to those events in turn.

The code in the execution stack \(synchronous tasks\) is always executed before reading the "task queue" \(asynchronous task\). Please see the example below.

> ```text
>
>     var req = new XMLHttpRequest();
>     req.open('GET', url);    
>     req.onload = function (){};    
>     req.onerror = function (){};    
>     req.send();
> ```

The req.send method in the above code is an Ajax operation that sends data to the server. It is an asynchronous task, meaning that the system will only read the "task queue" if all the code of the current script is executed. Therefore, it is equivalent to the following.

> ```text
>
>     var req = new XMLHttpRequest();
>     req.open('GET', url);
>     req.send();
>     req.onload = function (){};    
>     req.onerror = function (){};   
> ```

That is, specifying the parts of the callback function \(onload and onerror\) does not matter before or after the send\(\) method, because they are part of the execution stack, and the system always executes them before going to read the task queue. ".

### Five, timer

In addition to events that place asynchronous tasks, the Task Queue can also place timed events, which specifies how much time some code executes. This is called the "timer" function, which is the code that is executed periodically.

The timer function is mainly implemented by two functions: setTimeout\(\) and setInterval\(\). Their internal operation mechanism is exactly the same. The difference is that the code specified by the former is executed once and the latter is executed repeatedly. The following mainly discusses setTimeout\(\).

setTimeout\(\) takes two arguments, the first being the callback function and the second being the number of milliseconds to defer execution.

> ```text
>
> console.log(1);
> setTimeout(function(){console.log(2);},1000);
> console.log(3);
> ```

The result of the above code is 1, 3, 2, because setTimeout\(\) defers the second line to 1000 milliseconds.

If the second parameter of setTimeout\(\) is set to 0, it means that the specified callback function \(0 ms interval\) is executed immediately after the current code is executed \(execution stack is cleared\).

> ```text
>
> setTimeout(function(){console.log(1);}, 0);
> console.log(2);
> ```

The execution result of the above code is always 2, 1, because the system will only execute the callback function in the "task queue" after the second line is executed.

In short, the meaning of setTimeout\(fn,0\) is to specify that a task is executed at the earliest free time available to the main thread, that is, as early as possible. It adds an event at the end of the "task queue", so it will not be executed until the synchronization task and the "task queue" existing events have been processed.

The HTML5 standard specifies the minimum value \(the shortest interval\) of the second parameter of setTimeout\(\), which must be no less than 4 milliseconds. If it is lower than this value, it will increase automatically. Prior to this, older browsers set the minimum interval to 10 milliseconds. In addition, for those DOM changes \(especially those involving page re-rendering\), they are usually not executed immediately, but every 16 milliseconds. The effect of using requestAnimationFrame\(\) is better than setTimeout\(\).

It should be noted that setTimeout\(\) simply inserts the event into the "task queue" and must wait until the current code \(execution stack\) is executed, and the main thread will execute its specified callback function. If the current code takes a long time, it may take a long time, so there is no way to guarantee that the callback function will be executed at the time specified by setTimeout\(\).

### Six, Node.js Event Loop

Node.js is also a single-threaded Event Loop, but it works differently than the browser environment.

Please see the diagram below \(author [@BusyRich](https://twitter.com/BusyRich/status/494959181871316992) \).

![Node.js](http://www.ruanyifeng.com/blogimg/asset/2014/bg2014100803.png)

According to the above figure, the operating mechanism of Node.js is as follows.

> \(1\) The V8 engine parses JavaScript scripts.
>
> \(2\) The parsed code, call the Node API.
>
> \(3\) The [libuv library](https://github.com/joyent/libuv) is responsible for the execution of the Node API. It assigns different tasks to different threads, forming an Event Loop, which returns the execution result of the task to the V8 engine asynchronously.
>
> \(4\) The V8 engine returns the result to the user.

In addition to the setTimeout and setInterval methods, Node.js provides two other methods related to the "task queue": [process.nextTick](http://nodejs.org/docs/latest/api/process.html#process_process_nexttick_callback) and [setImmediate](http://nodejs.org/docs/latest/api/timers.html#timers_setimmediate_callback_arg) . They can help us deepen our understanding of the "task queue."

The process.nextTick method can trigger the callback function before the end of the current "execution stack" -- the next Event Loop \(the main thread reads the "task queue"\). That is, the task it specifies always occurs before all asynchronous tasks. The setImmediate method adds an event to the end of the current "task queue". That is, the task it specifies is always executed on the next Event Loop, much like setTimeout\(fn, 0\). Take a look at the example below \(via [StackOverflow](http://stackoverflow.com/questions/17502948/nexttick-vs-setimmediate-visual-explanation) \).

> ```text
>
> process.nextTick(function A() {
>   console.log(1);
>   process.nextTick(function B(){console.log(2);});
> });
>
> setTimeout(function timeout() {
>   console.log('TIMEOUT FIRED');
> }, 0)
> // 1
> // 2
> // TIMEOUT FIRED
> ```

In the above code, since the callback function specified by the process.nextTick method is always triggered at the end of the current "execution stack", not only the function A is executed before the callback function timeout specified by setTimeout, but also the function B is executed earlier than the timeout. This means that if there are multiple process.nextTick statements \(regardless of whether they are nested or not\), they will all be executed on the current "execution stack".

Now, look at setImmediate again.

> ```text
>
> setImmediate(function A() {
>   console.log(1);
>   setImmediate(function B(){console.log(2);});
> });
>
> setTimeout(function timeout() {
>   console.log('TIMEOUT FIRED');
> }, 0);
> ```

In the above code, setImmediate and setTimeout\(fn,0\) each add a callback function A and timeout, which are triggered in the next Event Loop. So which callback function is executed first? The answer is uncertain. The result may be 1--TIMEOUT FIRED--2 or TIMEOUT FIRED--1--2.

Confusingly, the Node.js documentation states that the callback function specified by setImmediate is always in front of setTimeout. In fact, this happens only when recursive calls.

> ```text
>
> setImmediate(function (){
>   setImmediate(function A() {
>     console.log(1);
>     setImmediate(function B(){console.log(2);});
>   });
>
>   setTimeout(function timeout() {
>     console.log('TIMEOUT FIRED');
>   }, 0);
> });
> // 1
> // TIMEOUT FIRED
> // 2
> ```

In the above code, setImmediate and setTimeout are encapsulated in a setImmediate, and its result is always 1--TIMEOUT FIRED--2, then function A must be triggered before timeout. As for the 2 rows behind TIMEOUT FIRED \(that is, function B is triggered after timeout\), because setImmediate always registers the event to the next round of Event Loop, so function A and timeout are executed in the same round of Loop, and function B is next. Round Loop execution.

We get an important difference between process.nextTick and setImmediate: multiple process.nextTick statements are always executed once in the current "execution stack", and multiple setImmediate may require multiple loops to execute. In fact, this is the reason why Node.js 10.0 adds the setImmediate method. Otherwise, recursively calling process.nextTick like this will be endless, and the main thread will not read the "event queue" at all!

> ```text
>
> process.nextTick(function foo() {
>   process.nextTick(foo);
> });
> ```

In fact, now if you write recursive process.nextTick, Node.js will throw a warning asking you to change to setImmediate.

In addition, since the callback function specified by process.nextTick is triggered in this "event loop", and setImmediate is specified in the next "event loop" trigger, it is obvious that the former always occurs earlier than the latter, and the execution efficiency Also high \(because you don't have to check the "task queue"\).  
  
[http://www.ruanyifeng.com/blog/2014/10/event-loop.html](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)

