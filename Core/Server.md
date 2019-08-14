# Server

To run your service methods, you will need to create a gRPC server. Firecomm extends the native gRPC-node `Server` class, so any and all methods found on the native class are supported by Firecomm's class.

The Firecomm `Server` class-instances have the following capabilities.
- Instantiate an arbitrary number secure and insecure sockets with `.bind()`
- Add your service definition and middleware functions with `.addService()`
- Fire up the server with `.start()`
- All other native gRPC `Server` class methods

```javascript
const { Server } = require( "firecomm" );
const server = new Server( );
```

## Server Constructor

First is to understand the constructor. The constructor takes in the following options. For more options for the config look [here](https://grpc.github.io/grpc/core/group__grpc__arg__keys.html).
- config
- error handler

#### Uncaught Error Handling

In vanilla gRPC, uncaught errors thrown inside of any method handler crashes the server instance.

An example Server with a config object and an error handler.

```javascript
const server = new Server(
  {GRPC_ARG_KEEPALIVE_TIME_MS: 500},
  (err,call) => {
    if (err) console.log(err);
    call.throw(err);
});
```

## Adding Services

Connects handlers and middleware functionality to your gRPC methods as defined in your `.proto` file. 

```javascript
const { Server } = require( 'firecomm' );
const server = new Server();
server.addService( 
  SERVICE, 
  RPC_METHODS_OBJECT, 
  SERVICE_MIDDLEWARE,
  ERROR_HANDLER 
);
```

## Bind

Invoke this method to connect your server instance to a channel. Allows for the creation of secure and insecure channels.

```javascript
const { Server } = require( 'firecomm' );
const server = new Server();
server.bind("0.0.0.0:8080")
```