# Stub

How to create a channel and invoke gRPC methods client side. 

```javascript
const { Stub } = require( "firecomm" );
const service = require("./service.js");
const sslCertificate = require("./sslCertificate.crt");
const stub = new Stub(
  service, 
  "localhost:3000", 
  { certificate: sslCertificate }
);

stub.sayHello( { greeting:"Hello world." } )
  .then( res => console.log( res ) );
  .catch( err => console.log( err ) );
```

The primary methods that are available on the stub.
  1.  The constructor, which will allow you to instantiate a channel for that service.
  2.  The methods that are available on an ordinary gRPC client/channel.
  3.  Your gRPC methods from the service you passed into that Stub, which have been extended by Firecomm with added functionality and simplified syntax;

# Constructor

The `Stub` constructor generates an instance of Firecomm's `Stub` class, which extends the native gRPC client-service instance.

```javascript
const { Stub } = require( "firecomm" );
const service = require("./service.js");
const sslCertificate = require("./sslCertificate.crt");
const stub = new Stub(
  service, 
  "localhost:3000", 
  { certificate: sslCertificate }
);
```