# Client Streaming Call
Client streaming calls generate a writable stream, where you can send an arbitrary number of messages to your server.

```javascript
const { Stub } = require( "firecomm" );
const service = require("./service.js");
const stub = new Stub(
  service, 
  "localhost:3000"
);
const stream = stub.sayHello( );
stream.write({greeting: "Hello world."})
```

## parameters

> NOTE: Client unary calls are top-level methods stored on the `Stub` instance. Method names match either the exactly Pascal-cased or Camel-cased name, as they appear in your Service definition from the `.proto` file.

1. ### [ *optional* ] Metadata `object` // object to be sent in Metadata of call
   ```javascript
   stub.sayHello(
     null, 
     {
       metadataProperty: "metadataValue"
     }
    )
   .then(res => console.log(res))
   .catch(err => console.log(err)); 
   )
   ```
2. ### [ *optional* ] Interceptor Provider Array `array` // array of Interceptor Providers to pass to the call
   ```javascript
   const interceptorProvider = require("./interceptors/interceptorProvider.js")
   stub.sayHello(
     {greeting: "Hello world."}, 
     [interceptorProvider]
     )
   .then(res => console.log(res))
   .catch(err => console.log(err)); 
   )
   ```
3. ### [ *optional* ] Callback `function` // callback to handle the error and response from the server. If none is supplied, the method invocation will return a `Promise` object.
   ### parameters
   1. #### ERROR `object` // an instance of `Error` returned from the server
   2. #### MESSAGE `object` // gRPC `Message` formatted object to match the messsage response belonging to the gRPC method
   ```javascript
   const interceptorProvider = require("./interceptors/interceptorProvider.js")
   stub.sayHello(
     {greeting: "Hello world."}, 
     null, 
     {
       metadataProperty: "metadataValue"
     },
     (err, res) => {
       if(err) console.log(err);
     }
    
    )
   )
   ```
## returns Writable stream `object` // a writable stream which connects to the server
  ### methods

1. #### .emit( EVENT, DATA ) `function`
      Inherited from the Writable stream object from Node.js. First parameter is a string indicating the event type like "*data*", second parameter is the data to be written to the stream.

   #### parameters
     1. #### EVENT `string` // a string representing the event type
     2. #### DATA `any` // data to be written to the stream

    #### returns `undefined`
    ```javascript
    const clientStreamHandler = function(call) {
      call.emit("customEvent",{
        eventData: "Hello world"
      });
    ```
2. #### .write( MESSAGE ) `function`
      Writes a message to the writable stream. Message should match the message type for the gRPC server-streaming method the handler is written for.

   #### parameters
     1. #### Message `object` // object following the format of the message definition in your `.proto` file.

    #### returns `undefined`
    ```javascript
    const clientStreamHandler = function(call) {
      call.write({
        greeting: "Hello world."
      })
    ```