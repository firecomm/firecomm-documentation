# Server Streaming Call
Server streaming calls generate a readable stream, where you can send a message to your server as well as listen for an arbitrary number of response messages.

```javascript
const { Stub } = require( "firecomm" );
const service = require("./service.js");
const stub = new Stub(
  service, 
  "localhost:3000"
);
const stream = stub.sayHello( { greeting: "hello world" } );
stream.on("data", data => {
  console.log( { data } );
})
```

## parameters

> NOTE: Server-streaming calls are top-level methods stored on the `Stub` instance. Method names match either the exactly Pascal-cased or Camel-cased name, as they appear in your Service definition from the `.proto` file.

1. ### Message `Message` // the message type as defined in your `.proto`
   ```javascript
    const stream = stub.sayHello({ greeting: "Hello world." })
    stream.on("data",(data) => console.log(data));
    stream.on("error", (err) => console.log(err));
   ```
2. ### [ *optional* ] Metadata `object` // object to be sent in Metadata of call
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
3. ### [ *optional* ] Interceptor Provider Array `array` // array of Interceptor Providers to pass to the call
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

## returns Duplex stream `object` // a readable/writable stream which connects to the server
  ### methods

1. ### .on( EVENT, CALLBACK ) `function`
      Inherited from the Readable stream object from Node.js. First parameter is a string indicating the event type like "*data*", second parameter is a callback to handle emitted data.

   ### parameters
     1. #### EVENT `string` // a string representing the event type
     2. #### CALLBACK `function` // a function to handle any data emitted from the Readable stream
        #### parameters
        1. ##### DATA `any` // emitted data
  

    ### returns `undefined`
    ```javascript
    const serverStreamHandler = function(call) {
      call.on("data",(data)=>{
        console.log(data);
    });
    ```