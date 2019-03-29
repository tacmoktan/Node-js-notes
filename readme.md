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