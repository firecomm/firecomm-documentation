# Server

The firecomm Server class is how you set up the logic to handle your various gRPC methods, bind to ports, add healthcheck.

```javascript
const { Server } = require( "firecomm" );
const server = new Server( );
```

First is to understand the constructor. The constructor takes in the following options. For more options for the config look [here](https://grpc.github.io/grpc/core/group__grpc__arg__keys.html).
- config
- error handler

## Uncaught Error Handling

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

In gRPC, adding service is the 

