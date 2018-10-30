# Streams
You can think about Streams as a great way to move data from point A to point B. It is even more helpful when the data you want to move is very large and you need a good way to do it. 

Hance, Streams. Streams allow you to move very large sets of data by send them chunk by chunk and that way instread of dealing with very large data, you deal with small chunks of it.

What if I tell you that you using Streams all the time? Well, you do. Streams are everywhere in Node. When you send `response` or getting `request` on your severe for example - you get or send a stream! How about when you reading file using `fs.readFile`? well, you read it as stream. 

The coolest thing imo about streams is that they are all based on [Events](/lib/Events)! Streams work by sending chunks of data, every chunk it `emit` an event so the program will know there is chunk of data sent. It will do it chunk by chunk up to the last chunk of data and then it will end the process with an `end` event so we know the stream was complete!


## 4 types of streams
We have 4 types of streams and we will go over them briefly.

* Readable - Streams you can read from 
* Writable - Streams that you can write into.  
* Duplex - Writable and Readable (`net.Socket`)
    * Transform - Which are like Duplex only can transform and modify the data as they being send. (`zlib.createDeflate()`)


### Readable
Readable streams are stream you create and can read data from. Take the next snippest for example:

```js
const fs = require("fs");
const readableStream = fs.createReadStream('source.png');

readableStream.on("data", (chunk) => {
    //do something with the chunks
})

readableStream.on("end", () => {
    //do something with the complete stream
})

```

What we are doing here is creating readable stream object from the `source.png` file.



### Writable
Writable streams are streams you create so you can write data into them. Consider the next snippets:

```js
const fs = require("fs");
const readableStream = fs.createReadStream('source.png');
const writableStream = fs.createWritableStream('target.png');

readableStream.on("date", (chunk) => {
    //do something with the chunks
    writableStream.write(chunk)
})

readableStream.on("end", () => {
    //do something with the complete stream
})

```
Here we took the old snippets and simply added the `fs.createWritableStream` to it. Now we take each chunk of data that we reading from `writableStream` and writing it to `writableStream` and with that data it will create the `target.png` file. 

## Duplex

`net.Socket` is a great example for for stream which is Readable and Writable. 

```js
const net = require("net")
const chat = net.Server()
let connections = []
chat.on("connection" , (socket) => {
    console.log(socket, "connected")
    connections.push(socket)
    socket.on("data" , (d) => {
        for (let i = 0; i < connections.length; i++) {
            if (connections[i] == socket) continue;
            connections[i].write(`${i}:${d}`)      
        }
    })
    socket.on("end", () => {
        const disconnectedUser = connections.indexOf(socket)
        connections.splice(disconnectedUser, 1)
        console.log(disconnectedUser, "disconnected")
    })
})

chat.listen(8000, () => console.log("listening"))
```

This example demonstrate how `socket` is a Readable and Writable Stream. Each  `socket` can write data into it to broadcast to the rest of the sockets connected to the server. These servers can of course use this data because as we said each socket is also readable. 

On `socket.on` we read the data coming into our socket, and on `connections[i].write` we are writing data to this socket. 