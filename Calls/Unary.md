# Unary Call `class`

A Unary Call class. Extends the `ServerUnaryCall` object properties and methods from gRPC-Node, thus all native methods and properties are still available on the call object.

```javascript
const unaryHandler = function(call) {
  console.log(call.req.meta);
  call.setMeta({
    'myProperty':'myValue'
  });
  call.send({
    message: "Hello world"
  })
}
```

### properties

1. #### .req `function` // an object containing information sent from the client
   #### properties
   1. ##### meta `object` // an object containing the unary request metadata sent from the client
   2. ##### body `object` // an object containing the unary request body sent from the client
2. #### .setMeta() `function`

### methods

1. #### .setMeta( METADATA ) `function`
    Sets metadata to be sent with the response. Takes in a JSON object of keys and values.
   #### parameters
     1. ##### METADATA `object` // a JSON object of properties and values that will get assigned and sent as metadata

    #### returns `undefined`
2. #### .throw( ERROR ) `function`
      Ends the request-response cycle and sends Error message to the client.
   
   #### parameters
     1. ##### ERROR `Error` // an instance of `Error` containing the error to be sent to the Client

    #### returns `undefined`
3. #### .setStatus( METADATA ) `function`
      Adds metadata in the trailers associated with an error message.
   
   #### parameters
     1. ##### METADATA `object` // takes in a JSON object of properties and values that will get assigned and sent as metadata for an error message.

    #### returns `undefined`
4. #### .send( MESSAGE ) `function`
      Ends the request-response cycle, data is passed in through the parameter.

   #### parameters
     1. ##### MESSAGE `object` // an object matching the keys and properties of your gRPC method types.

    #### returns `undefined`






