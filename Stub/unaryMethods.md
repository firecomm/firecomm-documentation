# Client Unary Call
Client Unary calls are the non-streaming methods. In your `.proto` neither message type is designated as a stream.

## parameters

> NOTE: Client unary calls are made top level methods stored on the `Stub` instance. Method names match either the exactly Pascal-case or camel-cased name as they appear in your Service definition in your `.proto` file.

1. ### Message `Message` // 
2. [ *optional* ] ### Metadata `object` // object to be sent in Metadata of call
3. [ *optional* ] ### Callback `function` // callback to handle the error and response from the server. If none is supplied, the method invocation will return a `Promise` object.

## returns
  1. ### undefined
      ### OR
   2. ### Promise `Promise` // resolves to the value of return