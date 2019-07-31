# Client Unary Call
Client Unary calls are the non-streaming methods. In your `.proto` neither message type is designated as a stream.

```javascript
const { Stub } = require( "firecomm" );
const service = require("./service.js");
const stub = new Stub(
  service, 
  "localhost:3000"
);
stub.sayHello({greeting: "Hello world."})
.then(res => console.log(res))
.catch(err => console.log(err)); 

)
```

## parameters

> NOTE: Client unary calls are top-level methods stored on the `Stub` instance. Method names match either the exactly Pascal-cased or Camel-cased name as they appear in your Service definition in your `.proto` file.

1. ### Message `Message` // the message type as defined in your `.proto`
2.  ### [ *optional* ] Metadata `object` // object to be sent in Metadata of call
   ```javascript
   stub.sayHello(
     {greeting: "Hello world."}, 
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
4. ### [ *optional* ] Callback `function` // callback to handle the error and response from the server. If none is supplied, the method invocation will return a `Promise` object.
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
## returns
  1. ### undefined
      ### OR
   1. ### Promise `Promise` // resolves to the value of return