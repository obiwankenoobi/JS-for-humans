# Why Node?
Good title, but lets start first with:

## what is Node
> Node is a set of libraries on top of V8 
> 
> *Ryan Dahl*

Thanks for reading! 

No, but for real, Node is an abstract of multiple tools and libraries such as, Javscript engine (Google VM V8 in most cases), [libuv](https://github.com/libuv/libuv) module (which is actually the part which provides us with the [Event loop](./event_loop)) and other c++ binding code glues together to create the Node application. 

We won't touch these areas here but I just wanted to put it out there. the main use of Node is to create network based apps. You can do so much more with it but as Ryan Dahl says , it's designed for it and do it better then anyone. 

Node is [asynchronous](lib/asyncByDesign) and thanks to the Event loop allows us to do really cool things with it which in other languages like Python for example would be much harder to accomplish.

On a single threaded framework, you will have to think very hard how to use your resources because you have only this many threads and you have this much to do with them. 

Node gives us a better way. Its API combined with all its libraries gives us the ability to design our apps so much better. It "force" us to use an asynchronous design that allows us to use so much fewer resources to accomplish the same results we would with other frameworks. Let's try to make an example

consider the next piece of code written in Python:

```py
while(true):
    time.sleep(5)
    print("hello")
```

So this code will halt the thread for 5 seconds and print "hello" on every loop.
But what if now your boss comes at you and says "Ohh you know what? now I need you to also print "world" but every 2 seconds.

You have two options to do it.

First is to reuse the current thread you using for the first task and find some algorithm which crosses both 5 and 2 and print it in parallel.  

The second is to use another thread and write another `while` loop. Which force us to use more resources. 

How Node will deal with the same task? 
Well in Node as you know, we have only one thread. So we obviously can't allocate another one to do something else. But We don't have to. Node isn't designed that way! Let's write an example:

```js
while(true) {
    setTimout(() => console.log("Hello"), 5000)
    setTimout(() => console.log("world"), 2000)
}
```

`setTimout` is one of the Node API and great example for its asynchronous nature, what it does compare to `time.sleep` is instead of halt the thread, it pulls the task out of the thread and deals with it on the side in the Node API land and the thread never stops.

Why it's good? Well, it's a highly scalable design! It won't require us to use as much resource as you would in other frameworks. Nor you wouldn't have to find weird algorithms to reuse the first thread that runs the first task. You can see we didn't actually touched the first statement and never actually halt the program. They both run together in the Node API land.

In my opinion, this is the main benefit of using Node for network related apps. It's use Javascript as it's API, and it's asynchronous which is a great design for I/O based apps. I'm sure I could think about other benefits but - I think it's enough to consider it as your next tool to build your network based app.