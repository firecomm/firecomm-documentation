# Server-Streaming Call `class`

A Server-Streaming Call class. Extends the `ServerWritableStream` object properties and methods from gRPC-Node, thus all native methods and properties are still available on the call object.

```javascript
const serverStreamHandler = function(call) {
  console.log(call.req.meta);
  call.sendMeta({
    'myProperty':'myValue'
  });
  call.write({
    message: "Hello world"
  })
}
```

## properties

1. ### .req `function` // an object containing information sent from the client
   ### properties
   1. #### meta `object` // an object containing the unary request metadata sent from the client
   2. #### body `object` // an object containing the unary request body sent from the client

## methods

1. ### .sendMeta( METADATA ) `function`
    Sends metadata to the client. Takes in a JSON object and converts it into gRPC metadata.
   ### parameters 
     1. #### METADATA `object` // a JSON object of properties and values that will get assigned and sent as metadata

    ### returns `undefined`
    ```javascript
    const serverStreamHandler = function(call) {
     call.sendMeta({
       'myProperty':'myValue'
     });
    }
    ```
2. ### .throw( ERROR ) `function`
      Sends an Error message to the client.
   
   ### parameters
     1. #### ERROR `Error` // an instance of `Error` containing the error to be sent to the Client

    ### returns `undefined`
    ```javascript
    const serverStreamHandler = function(call) {
     call.throw(new Error('My error message.'))
    }
    ```
3. ### .setStatus( METADATA ) `function`
      Adds metadata in the trailers associated with an error message, sent with next call to `.throw()` 
      > NOTE: Must be called before `.throw()`;
   
   ### parameters
     1. #### METADATA `object` // takes in a JSON object of properties and values that will get assigned and sent as metadata for an error message.

    ### returns `undefined`
    ```javascript
    const serverStreamHandler = function(call) {
      call.setStatus({
        'details':'Error details here.'
      })
      call.throw(new Error('My error message.'))
    }
    ```
4. ### .emit( EVENT, DATA ) `function`
      Inherited from the Writable stream object from Node.js. First parameter is a string indicating the event type like "*data*", second parameter is the data to be written to the stream.

   ### parameters
     1. #### EVENT `string` // a string representing the event type
     2. #### DATA `any` // data to be written to the stream

    ### returns `undefined`
    ```javascript
    const serverStreamHandler = function(call) {
      call.emit("customEvent",{
        eventData: "Hello world"
      });
    ```
4. ### .write( MESSAGE ) `function`
      Writes a message to the writable stream. Message should match the message type for the gRPC server-streaming method the handler is written for.

   ### parameters
     1. #### Message `object` // object following the format of the message definition in your `.proto` file.

    ### returns `undefined`
    ```javascript
    const serverStreamHandler = function(call) {
      call.write({
        greeting: "Hello world."
      })
    ```