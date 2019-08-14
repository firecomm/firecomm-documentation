# Server

To run your service methods, you will need to create a gRPC server. Firecomm extends the native gRPC-node `Server` class, so any and all methods found on the native class are supported by Firecomm's class.

The Firecomm `Server` class-instances have the following capabilities.
- Listen on an arbitrary number secure and insecure sockets with `.bind()`
- Add your service definition and middleware functions with `.addService()`
- Fire up the server with `.start()`
- All other native gRPC `Server` class methods

## Server Constructor

```javascript
const { Server } = require( "firecomm" );
const server = new Server( );
```

First is to understand the constructor. The constructor takes in the following options.
- config (For more options for the config look [here](https://grpc.github.io/grpc/core/group__grpc__arg__keys.html).)
- error handler

#### Uncaught Error Handling

In vanilla gRPC, uncaught errors thrown inside of any method handler crashes the server instance.

An example Server with a config object and an error handler.

```javascript
const server = new Server(
  {GRPC_ARG_KEEPALIVE_TIME_MS: 500},
  (err,call) => {
    call.throw(err);
});
```

## Adding Services

Connects handlers and middleware functionality to your gRPC methods as defined in your `.proto` file. 

> NOTE: Any error handler declared at the service level will only handle errors for method handlers within that service. Error handler will overwrite and replace the any error handler defined in instantiation of the server.

```javascript
const { Server, build } = require( 'firecomm' );
const server = new Server();
const package = require("./builtPackageDefinition")
server.addService( 
  package.<Service Name>, 
  {
    <Method Name>: [<Middleware Function>, <Handler Function>]
  }, 
  [<Service Middleware Function]],
  (err, call) => {
    call.throw(err)
  }
);
```

> NOTE: Middleware functions are blocking. Service-level middleware functions are ran first in the middleware stack, method-level middleware functions are fun in the order they are indexed in the middleware stack array provided in the second argument to Server.addService().

## Binding Secure and Insecure Ports

Invoke this method to connect your server instance to a channel. Allows for the creation of secure and insecure channels. To create a secure SSL channel, pass a config object as the second argument with paths to your SSL certificate and private key.

```javascript
const { Server } = require( 'firecomm' );
const server = new Server();
let certPath = path.join(__dirname, "/server.crt");
let keyPath = path.join(__dirname, "/server.key");

const result = server.bind(["0.0.0.0:3000", "0.0.0.0:3001"], {
  privateKey: keyPath,
  certificate: certPath
});
server.bind("0.0.0.0:8080")
```