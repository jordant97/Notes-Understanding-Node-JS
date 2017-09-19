# Understanding-Node-JS
Notes on the course Understanding Node JS by Tony Alicea

## Classes in Javascript
- We understand that class in JS is just syntetic sugar to write a function constructor
```js
//Function constructor
function Police(firstname) {
    this.firstname = firstname;
}

//With classes
class Person {
    constructor(firstname) {
        this.firstname = firstname;
    }
}
```

- It replaces alot of things in function constructor
    - You dont need to use util.inherits anymore | It is now just extends after your class
    - You dont need initialise functions on prototype chain anymore | It is now just calling the function name in the class
    - You dont need to use .call(this) on the super class anymore | It is now just super()
```js

//The normal and old way
util.inherits(Police, Person);

function Police() {
    Person.call(this);
    this.badgesNumber = "1234"
}

Police.prototype.shoot = function() {
    console.log("Shooting");
}

//The class and new way
class Police extends Person {
    constructor(firstname) {
        super(firstname);
        this.badgesNumber = "1234"
    }

    //Function written in a class will be directly added to the prototype
    shoot() {
        console.log("Shooting");
    }
}

```
- However, it is still different from what we used to know about classes in other programming languages. This is still prototypical inheritence.

## Asynchronous Code, libuv, the event loop, stream files and more

### Javascript Aside
1. **Asynchronous**: More than one process running simultaneously.
Node does things asynchronously. V8 does not.

2. **Synchronous**: One process executing at a time.
Javascript is synchronous. Think of it as one line of code executing at a time.
NodeJS is asynchronous

## 7. HTTP and being a Web Server
### Protocol
- **A set of rules 2 sides agree on to use when communicating.**
- Both the client and server are programmed to understand and use that particular set of rules. It's similar to 2 people from different countries agreeing on a language to speak in.

### TCP/IP
- Computers communicate over the internet by obeying TCP/IP. (A protocol)
- IP (Internet Protocol): Every computer that are connected to the internet have an IP address. An address for computers so other computers can identified it.
- TCP (Transmission Control Protocol): Will split the data into chunks of packages. The data may be sent in different protocol (HTTP, FTP, SMTP...)
- The packets send in by TCP are in stream of packets, so we can use streams and chunk in Node to handle it.

### Socket
- Socket is the line across which information flow.
- The idea of a socket isnt new, it needs to be turn on and off for computers to send and receive data over the internet.
- When we talks about **socket**, we are not talking about **Web Socket**. Web Socket keeps the socket opens constantly so we can achieve real time communcation over the internet.

### Port
- Once a computer program receives a packet, how it knows what program to send it to.
- When a program is setup on the OS to receive packets from a particular port, it is said that the program is 'listening' to the port.
- Port have numbers assign to it (eg: port 80), and the number is then map to the program on the server (eg: node, email) , so the server know where to send the data to when it receive a request to the port number.

### HTTP
- A set of rules (and a format) for data being transferred on the web
- Stands for 'Hypertext transfer protocol'. It's a format (of various) defining data being transferred via TCP/IP.

### MIME Type
- A standard for specifying the type of data being sent.
- Stands for 'Multipurpose Internet Mail Extensions'. Examples: application/json, text/html, image/jpeg

### API
- A set of tools for building a software application.
- Stands for 'Application programming interface'. On the web the tools are usually made available via a set of URLs which accept and send only data via HTTP and TCP/IP

### Endpoint
- One URL in a Web API
- Sometimes that endpoint (URL) does multiple thing by making choices based on the HTTP request headers.

### Serialize
- Translating an object into a format that can be stored or transferred.
- JSON, CSV, XML, and others are popular. 'Deserialize' is the opposite (converting the format back into an object)

### Routing
- Mapping HTTP request to content.
- Whether actual files that exists on the server, or not.

### Template
- Template is text designed to be the basis for final text or content after being processed.
- There's usually some specific template language, so the template system knows how to replace placeholders with real values.
- We do templating manually in Node by using replace
- Turn the html into string first and use the replace method to turn the {template} into the variable string
```js
var header = "THis is a header';
html = html.replace('{header}', header);
```

### How to create a server with node
- This example is very popular in node because it is very easy and does alot of the work for us
```js
var http = require('http');
var fs = require('fs');

var server = http.createServer(function(req, res) {
    switch(req.url) {
        case "/":
            res.writeHead(200, 'Content-Type': 'text/html');
            fs.createReadStream(__dirname + '/index.html').pipe(res);
        break;

        case "/api":
            res.writeHead(200, 'Content-Type': 'application/json');
            var obj = {title: "3 idiots", author: 'Indians'};
            res.end(JSON.stringify(obj));
        break;

        default:
            res.writeHead(400);
            res.end();
        break;
    }
});

server.listen(8000, '127.0.0.1');
```

## 8. NPM and Node Package Manager
### Package
- Package is just a collection of code that you can use it in your code
- Managed and maintained with a package management system

### Package management system
- A management system is a software that automates installing and updating packages.
- Deals with what version you have or need, and manages **dependencies**.

### Dependency
- Code that another set of code depends on to function
- If you use that code in your app, it is a dependency. Your app depends on it.

### Versioning (Semantic versioning)
- Specifying what version of a set of code this is
- so others can track if a new version has come out. This allows to watch for new features, or to watch for 'breaking changes'
- The word 'semantic' implies that something conveys meaning
- Eg: Major.Minor.Patch (1.7.3)

### What is dev dependencies?
- Dependecies are code that your app depends on to function properly.
- Dev dependencies are code that are the code that you only need when you are in development process. (Eg: testing, automation)

```js
npm install nodemon --save-dev
```

## 9. Express
### Environment variables
- Global variables specific to the environment (server) our code is living in.
- Different servers can have different variable settings, and we can access those values in code.

### HTTP method
- Specifies the type of action the request wishes to make
- GET, POST, DELETE, and others. Also called **verbs**.

### Middleware
- Code that sits between 2 layers of software
- In the case of Express, sitting between the request and the response.

### Static
- Not dynamic
- In other words, not processed by code in any way. For example HTML, CSS and image files are 'static' files.

### How to setup a server using Express
```js
var express = require('express');
var app = express();

//In the listen method, express is creating a server with the http module behind the scene
var server = app.listen(80, listening);

function listening() {
    console.log('Port 80 is listening...');
}

//express.static is a middleware
app.use('/asset', express.static(__dirname + '/public'));

//We can create a custom middleware
//A middleware takes in 3 params, next is for running the next middleware.
app.use('/custom', function(req, res, next) {
    console.log("Requested URL: " req.url);
    next();
})

//This is consider a middleware too
app.get('/', function(req, res) {
    req.send("hello world");
})
```