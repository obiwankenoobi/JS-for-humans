# Async by design
Have you ever asked yourself what is the difference between Node.js and other programming languages? Well for start - Node is not a programming language but it's the runtime of Javascript.

> Runtime describes software/instructions that are executed while your program is running, especially those instructions that you did not write explicitly, but are necessary for the proper execution of your code. 

Node is an obstract of multiple tools and libraries such as the Javascript engine (V8 in most cases) libuv module (which is actually the part which provides us with the [Event loop](./event_loop)) and other c++ binding code glues togeather to create the Node application. We wont touch these areas here but I just wanted to put it out there. 

So what is so special about Node? well, there is bunch og things! One of them is that Node is singel threaded and built with asynchronous aproach in mind! What it means is - Just like we know Javascript, Node can only do one thing at a time, this is great because of it'a asynchronous nature which is highly scalable architecture design! 
You dont need bunch of threads to deal with tasks! the Node API togeather with the Event loop deal it for us! 
This is awesome because we only have one thread and one Stack. We can't block it by making http requests or file reading which can take.. who know how long?! 

If we comapre Node to other multi threaded runtime enviroments such as .ASP for example, we can clearly see the benefits using single threaded one. In .ASP - if you getting bunch of tasks, http reqests and all of the goodies we have in web at once, in it's synchronous nature, the program will start to divide the tasks between all the threads it has available, which basically means, more people want to use it == more hardware you need so you could deal with the traffic. Why? well, it will look something like that: 

- "Ohh cool a task lets handle it, Ohh.. but there is another task, Hey you? can you handle this one? Im kind a doing something here.."

- "Sure give me that, Ohh dammit there is another task, Hey you? can you handle it for me? Im doing something!"

- "sure! oh wait.. there is.."

Well you get the idea. 


On the other hand, in Node and its asynchronous nature we will simply leave it for the event loop and our Node API and it will look something like that: 

- "Oh cool a task, lets throw it to the back to get handled, Ohh onther task, cool lets do it again, Oh another one?! god this is a lot of work.. Im so happy I can handle it!"

Cool?! I really think it is. 


Even though you can use asynchronous architecture in other runtime enviroments, it will requeire you to do more work to make it right, the cool thing about Node is it's asynchronous by design and really built for it! 






