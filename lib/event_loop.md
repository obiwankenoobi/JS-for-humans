# The event loop

The event loop is one of the magics behind Node and browser Javascript. There is a lot of very technical ways to describe the event loop and I am sure google would be happy to give you the super technical explanation. 

I will try to explain it in human language. 

The event loop is a tool, in node it's part of the browser env and in Node you can find it inside [libuv](http://docs.libuv.org/en/v1.x/) and it's live in our enviroment. To understand it's proposes you should be familiar with the Call Stack. 

The Call Stack is a part of the engine which run our Javascript code. In Chrome it will be the V8 engine, in other browsers it will be something else. The Call Stack is a data structure, it is similar to a queue except it follows a LIFO or last in first out order. It's all job is to invoke the top function on the stack. Node and Javascript are a single threaded, therefore - We have only one stack! So we can only run one task every time. Not like in other languages for example where we can simply create new thread to deal with more tasks all at once. It means when you have a code looking like this:

```js
function foo() {
    fee()
}
function fee() {
    faa()
}
function faa() {
    console.log("I finally called")
}

foo()
```

Your stack will look like this:
```js
2:faa()
1:fee()
0:foo()
```
On every return of a call your stack will pull the top call of it and move on to the next one. And it will look something like that:
```js
2:faa() // <== returned
1:fee()
0:foo()
```

```js
1:fee() // <== returned
0:foo()
```
```js
0:foo() //<== return
```
```js
// open stack
```

So I hope you now have a rough idea about the Stack and it proposes. 

Because Node and Javascript are single threaded, We only have one stack and we can only do one thing every time (I know I repeat myself but it is so important to understand). So.. what if we have a function that takes a lot of time to finish running? Well, we call it,blocking the stack. It means we executing a function that won't let the rest of our program to run until this function will finish running. Consider the next piece of code:

```js
function done() {
    console.log("im done")
}
function start() {
    console.log("im started")
}
function blockTheStack() {
    let i = 0;
    while(i < 1000000000) {
        i++
    }
}
function run() {
    start()
    blockTheStack()
    done()
}
run()
```
The program will have to wait until `blockTheStack` will finish loop 1000000000 and only then it will continue to run! Of course its a play example but in real life, you can have network calls or reading files and it can take some time. 

Lets see the stack:

```js
1:start() // <== "im started"
0:run()
```
```js
1:blockTheStack() // <== *waiting for 1000000000 loops* it can take a while hole on...
0:run()
```
```js
1:done() // <== "im done"
0:run()
```
```js
0:run // <== return
```
```js
// open stack
```

Obviously, this is an awful experience and you can't write code that makes your program to hang. So what can we do about it? this is exactly where the event loop shines and do what he born to do! So let's add a function that will help us to overcome this issue:

```js
function notBlockingTheStack() {
    setImmediate(blockTheStack)
}
```

I am sure you all heard about asynchronous programming. But WHAT DOES IT ACTUALLY MEAN? that's a great question. In Javascript and Node - we have useful tools which we get for free in our env to make our life easier. In the browser they usually called WebAPI, and in the server, c++ binding API. Both enviroments have different API and capabilities. Lets talk about the Node API. One of these tools in the there is `setImmediate`. `setImmediate` is asynchronous and takes a callback just like almost any other API in Node and what it does it do run outside of the stack, in the OS area and leaving our stack open. When it is finishing to run, it will push it's callback into the Task-Queue to wait to the stack to be open and run. 

The Task-Queue is basically a line of tasks we receiving back from the WebAPI, that wait for the stack to be open so they can go back on top of it, run and return. 

Now when we get to this part in our program - what the stack is going to do is to say: 

"ok `notBlockingTheStack` is called, nice put it on top. Oh, and `setImmediate` inside it get called too, put it on top of it as well. Now I will run `setImmediate` and pull it out. Then I will return because `notBlockingTheStack` doesnt have anythig else to do" 

The Event loop runs constantly when we have something going on in our program. It will wait for tasks to be finished running on the WebAPI or the c++ API and push them to the Task-Queue so they can get back to the stack, run and return. 

Now `setImmediate` pulled out of the stack to the c++ API area, instead of `setImmediate` it can be some other API like `http` request or `readFile`. When the API will finish its job and be ready to invoke its callback, it will push the callback to the Task-Queue - in our example it will be `blockTheStack`. 

We talked a lot, lets write some code:

```js
function done() {
    console.log("im done")
}
function start() {
    console.log("im started")
}
function blockTheStack() {
    let i = 0;
    while(i < 100) {
        i++
    }
    console.log("didnt blocked anything(:")
}

function notBlockingTheStack() {
    setImmediate(blockTheStack)
}

function run() {
    start()
    notBlockingTheStack()
    done()
}
run()
```

Pretty similar to the old piece of code - only now we use asynchronous programming and  `setImmediate` to leave the stack clean. So let's see how the stack will look like:

```js
1:start() // <== "im started" return
0:run()
```
```js
2:setImmediate(blockTheStack) // <== calling setImmediate(blockTheStack) return
1:notBlockingTheStack() 
0:run()
```
```js
1:notBlockingTheStack()  // return
0:run()
```
```js
1:done() // <== "im done" return
0:run()
```
```js
1:blockTheStack() // <== "didnt blocked anything(:" return
0:run()
```
```js
0:run() // <== return
```
```js
// open stack
```

I hope it was at least a little helpful, It's really not an easy concept at first but I can assure it get easier as you read more and more about it! I learned a lot about the event loop from [Philip Roberts talk](https://www.youtube.com/watch?v=8aGhZQkoFbQ) 
Please check it out! Its gold(:

