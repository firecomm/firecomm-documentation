# Server.addService( )

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

### parameters

1. #### SERVICE_DEFINITION `object` // Service as it is named on your `.proto` file. Is a property on the built package.
2. #### RPC_METHODS_OBJECT `object` // maps each `RPC_METHOD`	to its `HANDLER_FUNCTION` or `MIDDLEWARE_STACK`.
    > **NOTE**: For RPC_METHOD, each handler name must match the name of the RPC method as defined in the `.proto` file, or it will throw an error.
   1. ##### RPC_METHOD `property` // must match each `rpc Method` named in the `.proto`

      1. ##### HANDLER_FUNCTION `function` // function to handle the `rpc Method`
          ##### *OR*
      2. ##### MIDDLEWARE_STACK `array` // array of `function`(s) to handle the `rpc Method`. The main `HANDER_FUNCTION` to be run must be last in the array.
3. #### [ *optional* ] SERVICE_MIDDLEWARE `function` OR `array` // Either a middleware function or an array of middleware functions that will add to the front of the middleware stack for every method in the service.
4. #### [ *optional* ] ERROR_HANDLER `function` // function that handles uncaught errors thrown in any of your service method handlers for this service, overwrites the `Server` level error handler if one has been specified

   #### parameters

   1. ##### ERROR`object` // uncaught error thrown from one of your method handlers
   2. ##### CALL `object` // firecomm `Call` object from the request that threw the error

   #### returns `undefined`

### returns `undefined`

### example

```javascript
const { Server, build } = require( "firecomm" );

const package = build('./PROTO_PATH');

const middleware1 = (call) => {
  console.log(call.req.body);
}

const middleware2 = (call) => {
  console.log(call.meta);
}

const handler = (call) => {
  call.send({
    message: "hello world"
  })
}

const server = new Server();

server.addService( 
  package.serviceName, 
  { handlerName: [ middleware2, handler ] },
  middleware1,
  function( ERROR, CALL ) {
    if ( ERROR ) CALL.throw( ERROR );
  }
);
```