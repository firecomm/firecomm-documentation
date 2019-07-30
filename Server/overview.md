# `class` Server

To run your service methods, you will need to create a gRPC server. Firecomm extends the native gRPC-node `Server` class, so any and all methods found on the native class are supported by Firecomm's class.

The Firecomm `Server` class-instances have the following capabilities.
- Instantiate an arbitrary number secure and insecure sockets with `.bind()`
- Add your service definition and middleware functions with `.addService()`
- Fire up the server with `.start()`
- All other native gRPC `Server` class methods