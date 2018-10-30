# Events
Events are one of the most important parts of Node and crucial to understand. Everything in Node is Event driven and this what makes it so useful framework.

So what are Events? 
Events are part of Node and as I mentioned earlier they are all over it. 

Events are made of two parts, the listener you create to listen for when an event happened, and the event emitter which is the signal you make to let the listener know the event happened.
Most of the core methods of Node you Events to work. 

Let's take for example the `http` module. When we creating new `http` server using `http.createServer` what we are actually doing is creating a new event listener to listen for requests coming to the server and when they do, we could handle them with a proper response. 

Another good example is the `fs` module. By calling `fs.readFile` we actually creating an event listener which will be triggered by the `readFile` method by the time the file was readen and loaded to memory - so we can know it does and we will be able to use it at that moment. 

Toy example will be:
```js
const EventEmitter = require("events")
const emitter = new EventEmitter()

function logIt(x) {
    console.log(x)
}

emitter.on("log", logIt)

emitter.emit("log", "hello world")
```

This example is not very useful but it giving a nice idea about the possibilities of events. You can see here that we first `require` the `events` module and getting back the `EventEmitter` module. Then we create a new instance of it and attaching it to `emitter`. 
The next thing is we create a simple function which takes a parameter and log it to the console. 

Next step we attaching an event listener to the word "logIt" using the `on` method, the `on` method taking two parameter - The first parameter is the name of the event. The second parameter is the function to invoke when the event is triggered. So now "log" will be our signal to know when we want to call the function. and `logIt` will be the function to invoke.

Now we are using the `emit` method trigger an event. The `emit` method takes the name of the event as its first parameter, and then you can pass the arguments you need for your function to work. 

Another not-so-useful-toy-example will be to pass a callback to the `emit` method to do when this event is triggered. This can be helpful to make different operations on values you get along the life of your program.

```js
const EventEmitter = require('events')
const emitter = new EventEmitter()

emitter.on("logIt",logIt)

function logIt (cb) {
   cb()
}

function consoleIt(x) {
    console.log(x)
} 

emitter.emit("logIt", () => consoleIt("hello world"))
```



