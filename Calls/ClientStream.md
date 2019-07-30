# Client-Streaming Call `class`

A Client-Streaming Call class. Extends the `ServerReadableStream` object properties and methods from gRPC-Node, thus all native methods and properties are still available on the call object.

```javascript
const clientStreamHandler = function(call) {
  console.log(call.req.meta);
  call.setMeta({
    'myProperty':'myValue'
  });
  call.send({
    message: "Hello world"
  })
}
```

## properties

## methods

1. ### .setMeta( METADATA ) `function`
    Sets metadata to be sent with the response. Takes in a JSON object of keys and values.
   ### parameters
     1. #### METADATA `object` // a JSON object of properties and values that will get assigned and sent as metadata

    ### returns `undefined`
    ```javascript
    const clientStreamHandler = function(call) {
     call.setMeta({
       'myProperty':'myValue'
     });
    }
    ```
2. ### .throw( ERROR ) `function`
      Ends the request-response cycle and sends Error message to the client.
   
   ### parameters
     1. #### ERROR `Error` // an instance of `Error` containing the error to be sent to the Client

    ### returns `undefined`
    ```javascript
    const clientStreamHandler = function(call) {
     call.throw(new Error('My error message.'))
    }
    ```
3. ### .setStatus( METADATA ) `function`
      Adds metadata in the trailers associated with an error message. 
      > NOTE: Must be called before .throw();
   
   ### parameters
     1. #### METADATA `object` // takes in a JSON object of properties and values that will get assigned and sent as metadata for an error message.

    ### returns `undefined`
    ```javascript
    const clientStreamHandler = function(call) {
      call.setStatus({
        'details':'Error details here.'
      })
      call.throw(new Error('My error message.'))
    }
    ```
4. ### .on( EVENT, CALLBACK ) `function`
      Inherited from the Readable stream object from Node.js. First parameter is a string indicating the event type like "*data*", second parameter is a callback to handle emitted data.

   ### parameters
     1. #### EVENT `string` // a string representing the event type
     2. #### CALLBACK `function` // a function to handle any data emitted from the Readable stream
        #### parameters
        1. ##### DATA `any` // emitted data
  

    ### returns `undefined`
    ```javascript
    const clientStreamHandler = function(call) {
      call.on("data",(data)=>{
        console.log(data);
    });
    ```