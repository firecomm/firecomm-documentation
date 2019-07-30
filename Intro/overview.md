# FIRECOMM
> 1.0.0.alpha release

Framework extending the gRPC-Node framework. Standardized syntax for transpiling protoBufs into Node.js, creating servers, clients, and managing gRPC channels in distributed systems.

## Purpose

**gRPC-Node** has a complex API but does not ***document*** or ***support*** all of the features available in **gRPC-goLang** or **gRPC-Java**. We standardize the syntax to expose all ***existing*** features and extend ***undersupported*** features in the Node.js ecosystem. 

### Install
```javascript
npm i --save firecomm
```

## Getting Started
#### 1. Define a ***.proto*** file

Let's begin by creating a file named `exampleAPI.proto` that will live inside a `proto` folder. The `ProtoBuf` we define in this file will define the name of the `package`, the names of the `service`s, the `rpc` methods, what the client `Stub` sends, what the `Server` returns, and the structured data that is part of each `message`.

```protobuf
// proto/exampleAPI.proto
syntax proto3

package exampleAPI

service FileTransfer {
  rpc ClientToServer (stream File) returns (Confirmation) {};
  rpc ServerToClient (Confirmation) returns (stream File) {};
}

service HeavyMath {
  rpc UnaryExample (Math) returns (Math) {};
  rpc BidiExample (stream Math) returns (stream Math) {};
}

message Confirmation {
  bool status = 1;
  string comments = 2;
}

message File {
  bytes fileBuffer = 1;
}

message Math {
  double num = 1;
}
```

> Each `rpc` Method clearly defines request/response, client `Stub` to `Server` regardless of call type. For example:
> ```protobuf
> //    MethodName    Stub/request         Server/response
> rpc ClientToServer (stream File) returns (Confirmation) {};
> ```

#### 2. `build()` a `package`

Next let's use our `.proto` file to build a package for our `Server` and client `Stub`s. We can create a `package.js` file which will `export` a `package` containing the transpiled `service`s, `rpc` methods.

```javascript
// package.js
const { build } = require( 'firecomm' );
const path = require( 'path' );
const PROTO_PATH = './'
const package = build( PROTO_PATH, CONFIG_OBJECT );
module.exports = package;
```
***returns** a gRPC package **object** with* `SERVICE_DEFINITION`*s as properties*

#### 3. Create a server
```javascript
const { Server } = require( 'firecomm' );
const server = new Server();
```
***returns** a gRPC server instance **object***
#### 4. Define your `HANDLER_FUNCTION` for each `RPC_METHOD` and/or `MIDDLEWARE_STACK` functions for each `RPC_METHOD`
1. #### CALL *object* // call methods are specific to each `CALL_TYPE`. Possible `CALL_TYPE`s are `UNARY`, `CLIENT_STREAM`, `SERVER_STREAM`, and `DUPLEX`.
```javascript
exampleUnaryHandler( CALL ) {
  // single response
  CALL.send({ response: value });
};
exampleClientStreamHandler( CALL ) {
  // listeners for stream from client
  CALL.on('data', request => someFunctionality(request));
  // single response
  CALL.send({ response: value });
};
exampleServerStreamHandler( CALL ) {
  // some logic to warrant a streaming response
  CALL.write({ responseChunk: value });
};
exampleDuplexHandler( CALL ) {
  // listeners for stream from client
  CALL.on('data', request => someFunctionality(request));
  // some logic to warrant a streaming response
  CALL.write({ responseChunk: value });
};
module.exports = { 
	exampleUnary,
	exampleClientStream,
	exampleServerStream,
	exampleDuplex,
}
```
*doesn't **return** anything*
#### 5. Add each `SERVICE_DEFINITION` for the server to handle
#### parameters:
1. #### SERVICE_DEFINITION *object* // Service as it is named on your `.proto` file. **Is a property on the built package.**
2. #### RPC_METHODS_OBJECT *object* // maps each `RPC_METHOD`	to it's `HANDLER_FUNCTION` or `MIDDLEWARE_STACK`.
	#### RPC_METHOD *property* // must match each `rpc Method` named in the `.proto`
	#### HANDLER_FUNCTION *value* // function to handle the `rpc Method`
	#### *OR*
	#### MIDDLEWARE_STACK *value* // array of *functions* to handle the `rpc Method`. The main `HANDER_FUNCTION` to be run must be last in the array.
```javascript
const { Server } = require( 'firecomm' );
const server = new Server();
server.addService( SERVICE, RPC_METHODS_OBJECT );
```
*doesn't **return** anything*
#### 6. Bind the server `SOCKETS`
#### parameters:
1. #### SOCKETS *string* or *array* // string composed of IP_ADDRESS: PORT or an array of strings to bind multiple sockets
2. #### *optional* SECURITY_CONFIG_OBJECT *object* // object defining the security of the connection. Default is insecure. 
```javascript
const { Server } = require( 'firecomm' );
const server = new Server();
server.addService( SERVICE, RPC_METHODS_OBJECT );
server.bind( SOCKETS, SECURITY_CONFIG_OBJECT );
```
*doesn't **return** anything*
#### 7. Start the server
```javascript
const { Server } = require( 'firecomm' );
const server = new Server();
server.addService( SERVICE, RPC_METHODS_OBJECT );
server.bind( SOCKETS, SECURITY_CONFIG_OBJECT );
server.start();
```
*doesn't **return** anything*
#### 8.  Open a client Stub with a `SERVICE_DEFINITION` and `SOCKET`
#### parameters:
1. #### SERVICE_DEFINITION *object* // Service as it is named on your `.proto` file. **Is a property on the built package.**
2. #### SOCKET *string* // string composed of IP_ADDRESS: PORT
3. #### *optional* SECURITY_CONFIG_OBJECT *object* // object defining the security of the connection. Default is insecure. 
```javascript
const { Stub } = require( 'firecomm' );
const clientStub = new Stub( 
	SERVICE, 
	SOCKET, 
	SECURITY_CONFIG_OBJECT 
);
```
***returns** a gRPC stub instance **object***
#### 9.  Make `RPC_METHOD` requests from the `STUB`
#### parameters:

1.  #### MESSAGE _object_ // Must have the properties of the `message` defined to be sent as `REQUEST` in the `RPC_METHOD` as defined in this stub's `SERVICE`. The `RPC_METHOD` that exists on the `STUB` matches the name you gave for the `RPC_METHOD` in your built `.proto` file.
2. #### *only for* **`UNARY`** *and* **`CLIENT_STREAM`** CALLBACK *function* // function which runs on once `CLIENT` gets a `RESPONSE` from `SERVER`
```javascript
const { Stub } = require( 'firecomm' );
const clientStub = new Stub( 
	SERVICE, 
	SOCKET, 
	SECURITY_CONFIG_OBJECT 
);
clientStub.exampleUnary( MESSAGE, CALLBACK );
const clientStream = 
  clientStub.exampleClientStream( MESSAGE );
  // some logic to warrant a streaming response
  clientStream.write( MESSAGE );
const serverStream = 
  clientStub.exampleServerStream( MESSAGE );
  // listeners for stream from server
  serverStream.on( 'data', response => 
  someFunctionality(request));
const duplex = 
  clientStub.exampleDuplex( MESSAGE );
  // listeners for stream from server
  duplex.on( 'data', response => 
  someFunctionality(request));
  // some logic to warrant a streaming request
  duplex.write( 'data', response => 
  someFunctionality(request));
```
`CLIENT_STREAM`, `SERVER_STREAM`, and `DUPLEX` ***return** a stream **object***