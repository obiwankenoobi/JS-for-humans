# Why Node?
Good title, but lets start first with:

## what is Node
> Node is a set of libraries on top of V8 
> 
> *Ryan Dahl*

Thanks for reading! 

No, but for real, Node is an abstract of multiple tools and libraries such as, Javscript engine (Google VM V8 in most cases), [libuv](https://github.com/libuv/libuv) module which is the part which provides us with the [Event loop](./event_loop) and allow us to make asynchronous I/O poerations, openSSL library and other parts of Node contains c++ binding code. All glued togeather to creat Node. 

We won't touch these areas here but I just wanted to put it out there. The main use of Node is to create network based server side applications. You can do so much more with it but as Ryan Dahl says , it's designed for it and do it better then anyone. 

Node is [asynchronous](lib/asyncByDesign) and thanks to the Event loop allows us to do really cool things with it which in other languages like Python for example would be much harder to accomplish.

On a multi threaded framework, you will have to think very hard how to use your resources because you have only this many threads and you have this much to do with them. Not only that but to do multi threading bug free software is a pretty hard job.

Node gives us a better way. Its API combined with all its libraries gives us the ability to design our apps so much better. It "force" us to use an asynchronous design that allows us to use so much fewer resources to accomplish the same results we would with other frameworks. Let's try to make an example,
consider the next piece of code written in Python:

```py
import time
    while(true):
        time.sleep(5)
        print("hello")
```

So this code will halt the thread for 5 seconds and print "hello" on every loop.
But what if now your boss comes to you and says "Ohh you know what? now I need you to also print "world" but every 2 seconds.

You have two options to do it.

First is to reuse the current thread you using for the first task and find something like the lowest common denominator for 5 and 2 and write an algorithm for it to run in parallel.  

The second is to use another thread and write another `while` loop. Which force us to use more resources and it really not scaling so well. 

How Node will deal with the same task? 
Well in Node as you know, we have only one thread. So we obviously can't allocate another one to do something else. But We don't have to. Node isn't designed that way! Let's write an example:

```js
setInterval(() => console.log("Hello"), 5000)
setInterval(() => console.log("world"), 2000)
```

`setInterval` is one of the Node API and great example for its asynchronous nature, what it does compare to `time.sleep` is instead of halt the thread, it takes the task you want to do as callback and pull it out of the thread and start 2 timers on the OS and it will push each function to the task queue every 2 and 5 seconds.

Why it's good? Well, it's a highly scalable design! It won't require us to use as much resource as you would in other frameworks. Nor you wouldn't have to find weird algorithms to reuse the first thread that runs the first task. 

In our example, you can see we didn't actually touched the first statement and never actually halt the program. We simply run two timers on the OS side.

To understand how does it work and what actually happans you can check the [Event loop](./event_loop) chapter. 

In my opinion, this is the main benefit of using Node for network related apps. It's use Javascript as it's API, and it's asynchronous which is a great design for I/O based apps. I'm sure I could think about other benefits but - I think it's enough to consider it as your next tool to build your network based app.

## why not Node?
Well, if you need very machin or time complexity efficiency for example, you might want strict language as these behave much better with compilers and their time complexity is much easier to predict. 

If you want much more control about the processes and threads, you might go with multi threaded option. It will be good for crazy math and statistics works on multi core huge machines it will be really useful. As in Node even though its c++ cinding is multi threaded - there is limitations to it and it can't achive the same results as mult threaded one. 

But for networking I/O applications imp Node is the best option without doubt! 