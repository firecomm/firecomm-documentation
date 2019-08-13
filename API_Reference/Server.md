# firecomm.Server

## Constructor
```javascript
new firecomm.Server([options])
```
parameters:
| Name    | Type   | Description                                                                                                             |
|---------|--------|-------------------------------------------------------------------------------------------------------------------------|
| options | Object | Options that should be passed to the internal server implementation. The available options are listed in Google's grpc-core documentation. |
returns `Server`
## Methods

### .addService(serviceName)
| Name              | Type/Properties     | Values          | Description                                                                                                                                                                     |
|-------------------|---------------------|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| serviceName       | package.ServiceName |                 | The serviceName you're adding will exist as a property on the `package` returned from `firecomm.build()`                                                                        |
| implementation    | Object              |                 | RPC methodNames as properties and handlers/middleware stacks as values.                                                                                                         |
|                   | RPCmethodName       | handlerFunction | Function to be passed `Server Unary`, `Server Client-Stream Response`, `Server Stream`, or `Server Duplex` based on RPC method definition in `proto`.                           |
|                   |                     | middlewareArray | Array of functions from index 0 up to be passed `Server Unary`, `Server Client-Stream Response`, `Server Stream`, or `Server Duplex` based on RPC method definition in `proto`. |
| serviceMiddleware | Array               |                 | Array of functions from index 0 up to be passed `Server Unary`, `Server Client-Stream Response`, `Server Stream`, or `Server Duplex` based on RPC method definition in `proto`. |
| errorCallback     | Function            |                 |                                                                                                                                                                                 |
returns `undefined`



