# Server

To run your service methods, you will need to create a gRPC server. Firecomm extends the native gRPC-node `Server` class.

The Firecomm `Server` class has the following capabilities.
- Create a new `Server` instance with the constructor
- Instantiate a socket with `.bind()`
- Fire up the server with `.start()`
- Add your service definition and middleware functions with `.addService()`
- All other native gRPC `Server` class methods

# Server Creation

```javascript
const {Server}  = require('firecomm');
const server = new Server();
```

# Adding a Service

# Adding a Service

# Creating a Socket

