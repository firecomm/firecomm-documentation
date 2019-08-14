# Getting Started
## Install
``` 
npm i --save firecomm
```

## 1. Define a `.proto` file
Let's begin by creating a file named `exampleAPI.proto` that will live inside a `proto` folder. The `ProtoBuf` we define in this file will define the name of the `package`, the names of the `service`s, the `rpc` methods, what the client `Stub` sends, what the `Server` returns, and the structured data that is part of each `message`.

```protobuf
// proto/exampleAPI.proto
syntax = 'proto3';

package exampleAPI;

service ArrayTransfer {
  rpc ClientToServer (stream Array) returns (Confirmation) {};
  rpc ServerToClient (Confirmation) returns (stream Array) {};
}

service HeavyMath {
  rpc UnaryMath (Math) returns (Math) {};
  rpc BidiMath (stream Math) returns (stream Math) {};
}

message Confirmation {
  bool status = 1;
  string comments = 2;
}

message Array {
  repeated Math = 1;
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

## 2. Let's `build()` a `package`

Now that we've defined our API in a ProtoBuf, let's pass an absolute path to our `.proto` file to build a `package`. We will create a `package.js` file which will live in our root folder and `export` a configured `package` containing the transpiled `service`s and `rpc` methods.

We will also define our configuration for how our packaged methods will handle different data types.

```javascript
// package.js
const { build } = require( 'firecomm' );
const path = require( 'path' );
const PROTO_PATH = path.join( __dirname, './proto/exampleAPI.proto' );

const CONFIG_OBJECT = {
  keepCase: true, // keeps everything camelCased
  longs: Number, // transpiles the enormous `double`s for our HeavyMath into a javascript Number rather than a String
  bytes: String, // helps us manage the FileTransfer bytes as javascript `String`s rather than pure hexadecimal Buffers or uint8Arrays
}
const package = build( PROTO_PATH, CONFIG_OBJECT );
module.exports = package;
```

> Advanced Note: whether you're building a firecomm/gRPC-Node `Server`, a firecomm/gRPC-Node client with `Stub`s, or a firecomm/gRPC-Node hybrid client/server, it is important to build a package with configurations that match the API for your distributed system. Every server and client should have the same `.proto` file regardless of language.

## 3. Create a server
Next, we will create a `new Server()` inside a `server.js` file which will live in a `server` folder. 

```javascript
// /server/server.js
const { Server } = require( 'firecomm' );
const server = new Server();
```
## 4. Define the server-side handlers for our `ArrayTransfer` service.

Let's define handler functions for our two `ArrayTransfer` `rpc` methods. Method handler functions will contain the server-side logic for our `service`s. Let's create a `arrayTransferHandlers.js` file which will live inside our `server` folder.

```javascript
// /server/arrayTransferHandlers.js
ClientToServerHandler( onClientStream ) {
  let resArray = [];
  onClientStream.on('data', (array) => {
    resArray = resArray.concat(array);
  })
  onClientStream.on('end', () => {
    onClientStream.send({ 
      status: true,
      comments: 'response from server: the client stream is complete',
      array : resArray,
    });
  })
};
ServerToClientHandler( onClientCall ) {
  let count = 0;
  setInterval(() => {
    count += 1;
    onClientCall.write({array: [count, count / 2]})
  }, 1);
  
};
module.exports = { 
	ClientToServerHandler,
	ServerToClientHandler,
}
```

## 5. Define the server-side handlers for our `HeavyMath` service.

Let's define handler functions for our two HeavyMath methods. Let's continue by defining the handlers for our `HeavyMath` service in a `heavyMathHandlers.js` file which will live inside our `server` folder.

```javascript
// /server/heavyMathHandlers.js
UnaryMathHandler( CALL ) {
  CALL.send({ response: value });
};
BidiMathHandler( CALL ) {
  CALL.on('data', request => someFunctionality(request));
  CALL.send({ response: value });
};
module.exports = { 
	UnaryMathHandler,
	BidiMathHandler,
}
```

## 6. Add each `service` from the package to the `Server`

Let's now return to the `server.js` file and map each `service` onto our `Server`. Mirroring the structure of the `.proto` file we transpiled, the `package` object we built has both of the `service`s on it as properties. We use the `Server` method `.addService` to add the `services` one at a time and map each of the `rpc` methods to the handlers we wrote.  

```javascript
// /server/server.js
const { Server } = require( 'firecomm' );
const package = require( '../package.js' );
const { ClientToServerHandler,
	ServerToClientHandler } = require ( './fileTransferHandlers.js );
const { UnaryMathHandler,
	BidiMathHandler } = require ( './heavyMathHandlers.js );

const server = new Server();
server.addService( package.FileTransfer,   { 
  ClientToServer: ClientToServerHandler,
  ServerToClient: ServerToClientHandler,
 });
 server.addService( package.HeavyMath,   { 
  UnaryMath: UnaryMathHandler,
  BidiMath: BidiMathHandler,
 });
```
> Note: The `Server.addService()` method also allows the mapping of middleware functions or a middleware stack of functions in the form of an `array` to be passed in order to influence `rpc` methods before the handler which should come last in the array. For example: 
> ```javascript
> server.addService( package.HeavyMath,   > { 
>   UnaryMath: [ UnaryMathMiddleware, UnaryMathHandler ],
>   BidiMath: ServerToClientHandler,
> }, [ serviceLevelMiddleware1, serviceLevelMiddleware2 ]);
> ```

## 7. Bind the server to `socket`(s)

```javascript
// /server/server.js
const { Server } = require( 'firecomm' );
const package = require( '../package.js' );
const { ClientToServerHandler,
	ServerToClientHandler } = require ( './fileTransferHandlers.js' );
const { UnaryMathHandler,
	BidiMathHandler } = require ( './heavyMathHandlers.js' );

const server = new Server();
server.addService( package.FileTransfer,   { 
  ClientToServer: ClientToServerHandler,
  ServerToClient: ServerToClientHandler,
 });
 server.addService( package.HeavyMath,   { 
  UnaryMath: UnaryMathHandler,
  BidiMath: BidiMathHandler,
 });
server.bind('0.0.0.0: 3000');
```
> Note: `Server`s can be passed an array of `socket`s to bind any number of `socket`s. For example:
> ```javascript
> server.bind( [ 
>   '0.0.0.0: 3000', 
>   '0.0.0.0: 2999', 
> ] );
> ```
## 8. Start the server
```javascript
// /server/server.js
const { Server } = require( 'firecomm' );
const package = require( '../package.js' );
const { ClientToServerHandler,
	ServerToClientHandler } = require ( './fileTransferHandlers.js );
const { UnaryMathHandler,
	BidiMathHandler } = require ( './heavyMathHandlers.js );

const server = new Server();
server.addService( package.FileTransfer,   { 
  ClientToServer: ClientToServerHandler,
  ServerToClient: ServerToClientHandler,
 });
 server.addService( package.HeavyMath,   { 
  UnaryMath: UnaryMathHandler,
  BidiMath: BidiMathHandler,
 });
server.bind( [ 
  '0.0.0.0: 3000', 
  '0.0.0.0: 2999', 
] );
server.start();
```
> Run your new firecomm/gRPC-Node server with: `node /server/server.js`. It may also be worthwhile to map this command to `npm start` in your `package.json`.
## 9.  Create a `Stub` for the `FileTransfer` service:
Now that the `Server` is fully fleshed out, let's move to the client side by creating a client with `Stub`s for each `rpc` method on `ArrayTransfer`. Let's create a `arrayTransfer.js` file which will live inside our `clients` folder.
```javascript
// /clients/arrayTransfer.js
const { Stub } = require( 'firecomm' );
const package = require( '../package.js' )
const fileTransferStub = new Stub( 
	package.FileTransfer, 
	'localhost: 3000',
);
```
> In a real gRPC distributed system with firecomm/gRPC-Node clients, each client will most likely exist separately for each `service` defined in the shared `.proto` file. However, clients can actually have any number of `Stubs` running on them on either the same `socket` or multiple `sockets`. Additionally, duplicate clients running the same service(s) can be used to allow server level load-balancing.
## 10.  Make `ClientToServer` and `ServerToClient` service requests from the `Stub`

```javascript
// /clients/fileTransfer.js
const { Stub } = require( 'firecomm' );
const package = require( '../package.js' )
const fileTransferStub = new Stub( 
	package.FileTransfer, 
	'localhost: 3000',
);
const clientStream = 
  fileTransferStub.ClientToServer( MESSAGE );
  // some logic to warrant a streaming response
  clientStream.write( MESSAGE );
const serverStream = 
  fileTransferStub.ServerToClient( MESSAGE );
  // listeners for stream from server
  serverStream.on( 'data', response => 
  someFunctionality(request));
```
> Run your new firecomm/gRPC-Node client with: `node /clients/fileTransfer.js`. It may also be worthwhile to map this command to a custom command like `npm run transfer` in your `package.json`.

## 11.  Create a `Stub` for the `HeavyMath` service:
Now that the `Server` and `FileTransfer` Stub are fully fleshed out, let's create another `Stub` with access to each `rpc` method on `HeavyMath`. We'll create a `heavyMath.js` file which will live inside our `clients` folder.
```javascript
// /clients/heavyMath.js
const { Stub } = require( 'firecomm' );
const package = require( '../package.js' )
const heavyMathStub = new Stub( 
	package.HeavyMath, 
	'localhost: 2999',
);
```
> Note: two different clients *can* share a single socket on the server, in which case all concurrent requests and responses will be multiplexed. However, in a real gRPC distributed system, this is unlikely for two different services to share a socket.

## 12. Make `UnaryMath` and `BidiMath` service requests from the `Stub`
```javascript
// /clients/heavyMath.js
const { Stub } = require( 'firecomm' );
const package = require( '../package.js' )
const heavyMathStub = new Stub( 
	package.HeavyMath, 
	'localhost: 2999',
);
heavyMathStub.UnaryMath( MESSAGE );
  // some logic to warrant a streaming response
  clientStream.write( MESSAGE );
const bidiStream = 
  heavyMathStub.BidiMath( MESSAGE );
  // listeners for stream from server
  serverStream.on( 'data', response => 
  someFunctionality(request));
```
> Run your new firecomm/gRPC-Node client with: `node /clients/heavyMath.js`. It may also be worthwhile to map this command to a custom command like `npm run math` in your `package.json`.