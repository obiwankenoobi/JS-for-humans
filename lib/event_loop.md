# The event loop

The event loop is the magic behind Javascript. There is a lot of very technical ways to describe the event loop and I am sure google would be happy to give you the super technical explanation. 

I will try to explain it in human language. 

You can think about the event loop as an engine run nonstop on your program environment, to understand it proposes you should be familiar with the Stack. A stack is a data structure, it is similar to a queue except it follows a LIFO or last in first out order.. Javascript is a single threaded language, therefore can only run one task at once. It means when you have a code looking like this:

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
All the stack can do is to put calls on top of it, and pull calls from the top of it when they returned. So on every return of a call your stack will pull the top call of it and move on to the next one. And it will loop something like that:
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

Because Javascript is single threaded, We only have one stack and we can only do one thing every time. So.. what if we have a function that takes a lot of time to be done? Well, we call it, blocking the stack, it means we executing a function that won't let the rest of our program to run until this function will finish running. Consider the next piece of code:

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

I am sure you all heard about asynchronous functions. But WHAT DOES IT ACTUALLY MEAN? that's a great question. In Javascript - we have useful tools which we get for free and make our life so much easier. They usually called WebAPI, one of these tools is `setImmediate`. What it does is to take callback function as an argument and run it OUT OF THE MAIN STACK. 

Now when we get to this part in our program - what the stack is going to do is to say: 

"ok `notBlockingTheStack` is called, nice put it on top. Oh, and `setImmediate` inside it get called too, put it on top of it as well. Now I will run `setImmediate` and pull it out. Then I will return because `notBlockingTheStack` doesnt have anythig else to do" 

Here is where the magic happens - `setImmediate` is going to move the function that has been passed to it (in our example `blockTheStack`) to the side - and run it outside the stack so the stack stays open and ready to run other tasks! 

As we mentioned earlier, the Event loop runs constantly when we have something going on in our program. All it does is to wait for tasks to be finished running on the WebAPI and push them to the Task-Queue so they can get back to the stack, run and return. 

The Task-Queue is basically a line of tasks we receiving back from the WebAPI, that wait for the stack to be open so they can go back on top of it, run and return. 

Now `setImmediate` pulled out of the stack to the wepAPI area, in real life you will probably have there instead of `setImmediate` some other API like `http` request or `readFile`. When the API will finish its job and be ready to invoke its callback, it will push the callback to the Task-Queue - in our example it will be `blockTheStack`. 

In the Task-Queue out callback will sit and wait until the stack will be open - and when it does , it will be pushed on top of it and be ready to run and return.




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

Pretty similar to the old piece of code - only now we use asynchronous concept and  `setImmediate` to leave the stack clean. So let's see how the stack will look like:

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


