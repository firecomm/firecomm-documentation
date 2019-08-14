# Getting Started
## Install
``` 
npm i --save firecomm
```

## 1. Define a `.proto` file
Let's begin by creating a file named `exampleAPI.proto` that will live inside a `proto` folder. This file will define the name of the *Package*, the names of the *Services*, the *RPC methods* and the structured data of each *Message* sent and received.

```protobuf
// proto/exampleAPI.proto
syntax = 'proto3';

package exampleAPI;

service ChattyMath {
  rpc BidiMath (stream Msg) returns (stream Res) {};
}

message Msg {
  double req = 1;
}

message Res {
  double res = 1;
}
```

> Each *RPC Method* clearly defines the Message object to be sent and received. The Object we send will have the exact properties `peerSocket` with a string value and `req` or `res` with a value of `double`, which can be a Number or a String in JavaScript based on the build configuration.

## 2. Let's `build()` a `package`

Let's pass an absolute path to our `.proto` file to build our *Package*. We will create a `package.js` file which will live in our root folder and `export` an Object containing the compiled *Service* and *RPC method*.

```javascript
// package.js
const { build } = require( 'firecomm' );
const path = require( 'path' );
const PROTO_PATH = path.join( __dirname, './proto/exampleAPI.proto' );

const CONFIG_OBJECT = {
  keepCase: true, // keeps everything camelCased
  longs: Number, // compiles the enormous `double`s for our BidiMath Msg and Res into a JavaScript Number rather than a String
}
const package = build( PROTO_PATH, CONFIG_OBJECT );
module.exports = package;
```

## 3. Create a server
Next, let's construct a *Server* in a new `server` folder and file. 

```javascript
// /server/server.js
const { Server } = require( 'firecomm' );
const server = new Server();
```

## 4. Define the server-side handlers for our `ChattyMath` *Service*.

Let's define handler functions for our `BidiMath` *RPC method*. Server-Side handlers are how we will interact with the Client-side requests.

```javascript
// /server/chattyMathHandlers.js
function BidiMathHandler(bidi) {
  let start;
  let end;
  bidi
    .on('metadata', (metadata) => {
      start = Number(process.hrtime.bigint());
      console.log(metadata.getMap());
    })
    .on('error', (err) => {
      console.exception(err)
    })
    .on('data', (message) => {
      bidi.send({res: message.req});
      if (req % 10000 === 0) {
        end = Number(process.hrtime.bigint());
      console.log(
        'client address:', bidi.getPeer(),
        '\nnumber of requests:', req,
        '\navg millisecond speed per request:', ((end - start) /1000000) / req
      );
    }
  })
}

module.exports = { 
	BidiMathHandler,
}
```

## 5. Add each *Service* from the package to the `Server`

Let's go back to the `server.js` file and map each *Service* onto our `Server`. Mirroring the structure of the `.proto` file, the *Package* Object we built has each *Service* on it as properties. We use the `Server.addService` method to add each `Service` one at a time and map each *RPC method* to the handler we want to use.  

```javascript
// /server/server.js
const { Server } = require( 'firecomm' );
const package = require( '../package.js' );
const { BidiMathHandler } = require ( './chattyMathHandlers.js );

new Server()
  .addService( package.ChattyMath,   {
  BidiMath: BidiMathHandler,
})
```

## 6. Bind the server to sockets

```javascript
// /server/server.js
const { Server } = require( 'firecomm' );
const package = require( '../package.js' );
const { BidiMathHandler } = require ( './heavyMathHandlers.js' );

new Server()
  .addService( package.ChattyMath,   {
  BidiMath: BidiMathHandler,
})
  .bind('0.0.0.0: 3000')
```
> Note: `Server`s can be passed an array of `socket`s to bind any number of `socket`s. For example:
> ```javascript
> server.bind( [ 
>   '0.0.0.0: 3000', 
>   '0.0.0.0: 8080', 
>   '0.0.0.0: 9900',
> ] );
> ```
## 7. Start the server
```javascript
// /server/server.js
const { Server } = require( 'firecomm' );
const package = require( '../package.js' );
const { BidiMathHandler } = require ( './heavyMathHandlers.js' );

new Server()
  .addService( 
    package.ChattyMath,   
    { BidiMath: BidiMathHandler }
  )
  .bind('0.0.0.0: 3000')
  .start();
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