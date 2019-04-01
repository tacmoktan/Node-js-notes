# Node.js Notes
## Node.js
### 1. Introduction
* Node.js is runtime environment to run JavaScript on pc.
* was created embedding Chrome's v8 engine to c++ program.
* Every browser has a engine to understand js.

### 2. Node
* **window** & **document** objects are only available in browser not in node.
```js   
// browser        
window.onload();
window.setTimeOut();  
console.log(window);    /* works fine in browser but creates undefined error in node */
console.log(document);  
```
* Like **window** keyword, for browser js, **global** keyword represents *global object* for node js.
```js
// node
console.log(global);
```
**NOTE** : Creating global variables is a bad practice in browser js. And declaring global object in node is not possible instead we work on bunch of modules. ( Check out #5 Module Wrapper Function to know why )

```js
// node
let a = "globalOBJ";
console.log(global.a);  /* shows undefined */
```

### 3. Node Modules  
* Basically, any *program.js* file in node is module.
* **module** keyword represents module object in node. 
* **module** is not a global object. It needs to be imported and exported to other module.
```js
// node
console.log(module); /* logs a module object */
```

### 4. Export and Load a module  
* **Export module**  
Syntax
1. `module.exports.<alias> = <data>`  
exports whole *module object* with assigned `<data>` .
2. `module.exports = <singleData>`  
exports single *data* (number, string, function, etc.) from the module.

* **Load module**  
Syntax
1. `require('./<pathToTheModule>')`  
loads whole *module object* as well as single *data*.

e.g. 1

```js
    //logger.js
    let url = "some random url";
    
    function log(message){
        console.log(`URL = ${message}`);
    }
    
    module.exports.log = log;           //exports the logger module with log function within it
    module.exports.endPoint = url;      //exports the logger module with different alias for url
``` 
```js
    //app.js
    const logger = require ('./logger');    // 'require' loads the logger module 
    let log = logger.log;                   // accessing log function of logger module 
    let endPoint = logger.endPoint;         // accessing endPoint Data of logger module 
    log(endPoint);
```  
e.g.2
```js
    //logger.js
    let url = "some random url";
    
    function log(message){
        console.log(`URL = ${message}`);
    }
    
    module.exports = log;               //exports a single function log
```
```js
    //app.js
    const log = require('./logger');    //loads a single function log
    log('url message2');                
```
### 5. Module Wrapper Function  
* Data within module can't be global objects because every module is wrapped within a function by node js. 
* So inorder to access data within different modules we use `require('./<pathToModule>')`  

**Rough representation of how module is wrapped in a function.** 
```js
//what we see :V
console.log(__filename);
console.log(__dirname);

let url = "some random url";

function log(message) {
    console.log(`URL = ${message}`);
}

module.exports.log = log;
```

```js
//what it actually is :P
(function (exports, require, module, __filename, __dirname) {
    console.log(__filename);
    console.log(__dirname);

    let url = "some random url";

    function log(message) {
        console.log(`URL = ${message}`);
    }

    module.exports.log = log;
});
```
### 6. Path Module
* built-in module for node js. 
* provides several methods for working with file and directory paths. 
* The methods can yeild varying results according to OS used (Windows , POSIX).  
e.g.
 `path.parse(<pathString>)` => returns object for given path (string) like **"E:\\VSCode files\\NodePractice\\first-app\\app.js"**
```js
const path = require('path');           //loads path module  

const pathObj = path.parse(__filename); //returns an object using file directory input
console.log(pathObj);

/* //output 
{   root: 'E:\\',
    dir: 'E:\\VSCode files\\NodePractice\\first-app',
    base:'app.js',
    ext: '.js'm
    name: 'app'
} 
*/
```
### 7. OS module
* built-in module for node js.
* provides numerous useful OS related methods like `os.freemem()`, `os.totalmem()`, etc.  
e.g.  
```js
const os = require('os');           //loads operating system module

let freeMemory = os.freemem();      //returns free system memory in bytes as integer
let totalMemory = os.totalmem();    //returns total system memory in bytes as integer
console.log(`Free System memory = ${freeMemory}`);              
console.log(`Total System memory = ${totalMemory}`);
```

### 8. File System Module
* built-in module for node js.
* provides methods or API for interacting with file system. 
* File System operation/method types: **Synchronous** and **Asynchronous**.
* Asynchronous operation takes *call back function* as its last argument.  
e.g. `fs.readdir(<pathString>,<callBackFunction>);`
```js
const fs = require('fs');               //loads file system module

fs.readdir('./', function(err, files){    
    if (err) console.log("error",err)   // returns error msg
    else console.log("Result",files);   // returns array of files located in the directory
});
```
**NOTE**: Asynchronous operations are more efficient than synchronous ones.

### 9. Events Module
* built-in module.
* has a *EventEmitter* class that consist of several methods used for event handling.
* An instance of *EventEmitter* class is created to create event and register a listener(event handler).
```js
const EventEmitter = require('events');     //loads EventEmitter class
const emitter = new EventEmitter();         //creates instance of EventEmitter class

emitter.on('event',()=>{                    //registers a listener
    console.log('event triggered');
});

emitter.emit('event');                      //creates event
```
**NOTE:** Always register a listener first then create an event for it because when an event is created, it looks for the event listener registered before it.

### 10. Event Arguments
* Arguments can be passed in the call back function of listener.
* It's a good practice to pass arguments in object form.
```js
const EventEmitter = require('events');                 //loads EventEmitter class
const emitter = new EventEmitter();                     //creates instance of that class

emitter.on('event', (args) => {                         //registers a listener
    console.log('Listener called ',args);
});

emitter.emit('event', {id:1, name:'youtube', url:'http://'}); //creates event, passes arg in object form
```

### 11. Extending EventEmitter
* The EventEmitter can be extended by a custom class i.e. EventEmitter class properties can be inherited by a custom class. 
* This helps to create event within the class. The class can be exported and loaded to other module.
* Other modules can register event listeners for that event. 

e.g.
```js
//logger.js
const EventEmitter = require('events');         //loads EventEmitter class  

class Logger extends EventEmitter{              //Logger class inherits from EventEmitter class
    log(message, eventArgs){
        console.log('message = ',message);

        this.emit('event', eventArgs);    //to create event
    }
}

module.exports = Logger;    //exports Logger class
```
```js
//app.js
const Logger = require('./logger.js');         //loads Logger class
const loggerObj = new Logger();

loggerObj.on('event',(args)=>{                     //registering event listener
    console.log('Listener called ', args);
});

loggerObj.log('message', {id: 1, url:'http://'} );  //passing arguments to log function
```
### 12. HTTP module
* The module has methods that perform all http operations.
* Performing numerous amount of requests and responses can be complex.
* So we use **Express.js framework** for http operations.  
e.g. 
```js
const http = require('http');           //loads http module
const server = http.createServer( (req, res) => {   //creates server
    
    if(req.url === "/"){               
        res.write('home page');
        res.end();
    }

    if(req.url === "/api/courses"){ 
        res.write(JSON.stringify([ 1, 2, 3 ]));
        res.end();
    }
        
});

server.listen(1000);                //chooses server port no. 1000
console.log("Listening on port 1000...")
```