# Building a `ProtoBuf` package in Node.js

Whenever spinning up a `Server` or a client with `Stub`s, the first step is to `build` a package out of a `.proto` file.

```javascript
const { build } = require( 'firecomm' );
const path = require( 'path' );
const PROTO_PATH = path.join( __dirname, './example.proto' )

const package = build( PROTO_PATH );

module.exports = package;
```

## What is a `.proto` file?

Based on google's own documentation, we currently support `ProtoBuf`s written in `proto3` syntax.

> A `ProtoBuf` is an extensible mechanism shared across your entire API to define the `Services`, `RPC Methods`, and `Messages`. **Think of it like a schema for your API.**

A `.proto` file is a simple way to write out the `Package` your `Server` and client `Stub`s will be created from, as well as the `Service`s composed of `rpc Methods` which each request a `Message` and returns a `Message`.

```protobuf
syntax proto3

package exampleAPI

service ExampleService {
  rpc ExampleMethod ( ClientMessage ) returns ( HeavyMath ) {}
}

message ClientMessage {
  string request = 1;
}

message HeavyMath {
  sint64 mathresult = 1;
}
```

