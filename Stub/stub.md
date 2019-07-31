# Stub( ) `constructor`
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
## parameters

   1. ### SERVICE `object` // `.proto` packaged and built service definition in the form of a proto-loaded JS object
   2. ### SOCKET `string` // socket in the form of `IP`:`PORT`
   3. ### [ *optional* ] CONFIG `object` // if not provided,      
      ### options
      1. #### certificate: PATH `string` // file path to SSL certificate

## returns Stub `object` // an instance of the Firecomm Stub class