# Async by design
Have you ever asked yourself what is the difference between Node.js and other programming languages? Well for starters - Node is not a programming language. It's runtime environment for Javascript applications.

> Runtime describes software/instructions that are executed while your program is running, especially those instructions that you did not write explicitly, but are necessary for the proper execution of your code. 

Node is an abstract of multiple tools and libraries such as the Javascript engine (V8 in most cases) libuv module (which is actually the part which provides us with the [Event loop](./event_loop)) and other c++ binding code glues together to create the Node application. We won't touch these areas here but I just wanted to put it out there. 

So what is so special about Node? well, there is bunch of things! We will talk about one of them here. Node is single threaded and built with asynchronous approach in mind! What it means is - Just like we know Javascript, Node can only do one thing at a time, this is great because of it' an asynchronous nature which is highly scalable architecture design! 

You don't need a bunch of threads to deal with tasks! the Node API together with the Event loop deal with it for us! 
This is awesome because we only have one thread and one Stack. We can't block it by making HTTP requests or file reading which can take.. who know how long?! 

If we compare Node to other multi-threaded runtime environments such as .ASP for example - We can clearly see the benefits using a single threaded one. In .ASP - if you getting bunch of tasks, HTTP requests and all of the goodies we have in web at once, in its synchronous nature, the program will start to divide the tasks between all the threads it has available. It basically means, more people want to use it == more hardware you need so you could deal with the traffic. Why? well, it will look something like that: 

- "Ohh cool a task lets handle it, Ohh.. but there is another task, Hey you? can you handle this one? I'm kinda doing something here.."

- "Sure give me that, Ohh dammit there is another task, Hey you? can you handle it for me? I'm doing something!"

- "sure! oh, wait.. there is.."

Well, you get the idea. 


On the other hand, in Node and its asynchronous nature, we will simply leave it for the event loop and our Node API and it will look something like that: 

- "Oh cool a task, let's throw it to the back to get handled, Ohh another task, cool let's do it again, Oh another one?! god this is a lot of work... I'm so happy I can handle it!"

Cool?! I really think it is. 


Even though you can use asynchronous architecture in other runtime environments, it will require you to do more work to make it right, the cool thing about Node is it's asynchronous by design and really built for it! 






