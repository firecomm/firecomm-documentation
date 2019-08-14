# Calls

Firecomm method handlers and middleware access all core gRPC functionality for interacting with gRPC calls through a simplified Call object. 

When a gRPC server method is invoked, a new Call object is instantiated with the information for the request. It is passed through the middleware chain (a blocking operation) as the parameter to each function.

> NOTE: Firecomm middleware are asynchronous, and blocking by default, and side-effect the same Call object. Thus, changes to properties on the Call object from one middleware function are visibile by the next functions in the chain.

Depending on the gRPC method-type, different methods and properties are available on the Call object. There are four main types of Calls, one to match each of the gRPC method types:

- Unary Call
- Server-Side Streaming Call
- Client Side Streaming Call
- Bidirectional Call