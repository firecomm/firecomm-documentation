# firecomm.Stub

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

## Constructor

| Returned from   | Type   | Description                                                                                                                     |
| --------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------- |
| `Firecomm.Stub` | Object | The `Stub` constructor generates an instance of Firecomm's `Stub` class, which extends the native gRPC client-service instance. |

Parameters:

| Name    | Type/Properties | Values/Parameters | Description                                                                            |
| ------- | --------------- | ----------------- | -------------------------------------------------------------------------------------- |
| service | Object          |                   | `.proto` packaged and built service definition in the form of a proto-loaded JS object |
| socket  | String          |                   | socket in the form of `IP`:`PORT`                                                      |
| config  | Object          |                   | *optional*                                                                             |
|         | .certificate    | path              | `string` file path to SSL certificate                                                  |


returns `Stub` class.

Stub has all methods that can be found on the [gRPC Client](https://grpc.github.io/grpc/node/grpc.Client.html). Also has Stub Calls to match the service defined in your .proto.




