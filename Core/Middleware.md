# Middleware

Firecomm allows the injection of middleware at the method-level and the service-level for layering functionality. 

```javascript
const { Server } = require('firecomm');
const server = new Server();
const package = require("./builtPackageDefinition")
server.addService( 
  package.ExampleService, 
  {
    exampleMethod: [methodMiddleware1, methodMiddleware2, methodHandler]
  }, 
  [serviceMiddleware1, serviceMiddleware2],
  (err, call) => {
    call.throw(err)
  }
);
```

The following diagram depicts the order that call objects are passed through each of the functions in the middleware stack.

|         Call Object        |
|:--------------------------:|
|              I            |
|              I            |
|              V             |
| Service Level Middleware 1 |
| Service Level Middleware 2 |
|  Method-Level Middleware 1 |
|  Method-Level Middleware 2 |
|       Method Handler       |

- Middleware are blocking by-default using async/await. Thus, any asynchronous functionality inside of a method handler will stop the execution of the middleware stack.
- Middleware functions side-effect the call object. Thus changes to the call-object inside of one middleware function will persist and be visible by all middleware handlers invoked later in the middleware stack.