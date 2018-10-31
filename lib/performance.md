# performance
In a perfect world, we wouldn't have to worry about this topic and everything was optimized for us. Well, it isn't a perfect world. (:

In Node, there is a crucial part to know when it comes to performance - and it's how the async functions work behind the scene and what exactly is going on there. In this section, I will try to explain it in simple words without touching CS concepts so much so it stays in humans language.

We talked a lot about the [Event loop](/lib/Event_loop) and how it makes Node so amazingly fast and awesome. Well, the event loop indeed an awesome tool but we have the other part which makes it all possible and it's the c++ which allow ut to use the actual OS.

This API binding is the part behind the scene which does the actual heavy lifting when the event loop sends tasks there. This is not magic, there is an actual part in the CPU that work on these tasks! I know, it's crazy. 

Even though we talked a lot about how Node is actually single threaded and how awesome it is - behind the scenes, in our API land? we are not so single threaded after all. 

Yes, shock. 

When tasks move from our single threaded application it actually moves to be handled by the system kernel (OS) and it will do it with multiple threads! Even though this part is completely out of our sight, it does work that way and it is important to understand the basic mechanism of it.

What are threads anyway? 
You can think about threads as units on top of the process which each of them have its own stack and it's stack handle tasks given to it. This is a really basic explanation though.

By default, Node gives us pool of 4 threads to work with so let's see how it works:

```js
const fs = require("fs")

for(let i = 0; i < 4 i++) {
    fs.readFile("dummy.png")
}
```

As we know `fs` is a core module of Node and as we were told, it is an async method. But is it really? 

What this code is going to do is loop 4 times and each time it will go on the stack just for the event loop pull it out to the OS to be handled by our c++ API. We have 4 threads there and each thread will handle one of the tasks and they will all finish around the same time because of our multi-threaded system and their parallel work, and this is cool.

But how about this:

```js
const fs = require("fs")

for(let i = 0; i < 6 i++) {
    fs.readFile("dummy.png")
}
```

Here we have a tricky situation. We have 6 tasks to do and only 4 threads available. What is going to happen? Well, the 4 first tasks will start and the other two will have to wait. All our threads blocked and can't handle any more tasks! When the first 4 tasks will finish and move to the queue, the other two could start work too. This is a really important piece to realize! We have limitations and we must know them to properly work with them. 

It is actually one of the bottlenecks we have in Node and even though in theory we could increase the threads pool, it will be kind of a hustle. This is interesting because we can actually see in what scenarios more control of the thread will be helpful, so keep it in mind.

How about the next example:

```js
const http = require("http")

const doHTTPRequest = https.request("https://google.com", (res) => {
    res.on("data", () => {}) 
    res.on("end", () => {}) 
}).end()

for (let i = 0; i < 6; i++) {
    doHTTPRequest()
}
```

What will happen here? I assume you all guess it will do basically the same thing as before and it will deal the tasks between the threads and wait until the threads will be open to deal with the other two, right? Wrong! Sorry, I'm excited. 

New thing to learn about Node and about OS in general, not all tasks needs a thread to run. There are tasks which actually take very little effort and can be done in a real async manner by the kernel. One of these tasks is `http.reqest`. So you can handle a huge amount of requests in a real async way and they all can be handled in parallel! 

This makes Node ideal for network-based application and makes the scaling process so much simpler.


## Threaded pool
* fs.*
* dns.lookup
* pipes(edge cases)
* Windows only:
    * Child process
    * TTY input
    * TCP servers (edge cases)



## kernal async
* TCP/UDP servers and clients
* pipes
* dns.resolveXXX
* *NIX only:
    * UNIX domain sockets
    * TTY inputs
    * *NIX signals
    * Child process


The lists above will let you know which process need threads, and which can work on the kernel. It will help you design your program better and allow you to understand better your application performance. Please note there is scenarios which are unique to each OS, it will let you understand how your program will act on *NIX or on Windows so you can make it more robust based on that.


So this is Node performance in nutshell. I didn't get too technical, but this is a rough idea about how Node works behind the scenes and I hope it will help you better design your next program! For more technical explanations please check the [README.md](/README.md) at the end for my resources for this book. 