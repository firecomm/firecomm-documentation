# Defining `Calls` in your `.proto`

You define the behavior of your `rpc Methods` inside each of your `service`s in your `.proto` file.

There are four possible types of `Calls` definable in the `ProtoBuf`. These types are delineated by `stream`.
```protobuf
syntax proto3

package exampleProtoBuf

service FileTransfer {
  // `Unary` Call
  rpc OneByteUnary (Chunk) returns (Chunk) {};
  // `Client Stream` Call
  rpc ClientToServer (stream Chunk) returns (Chunk) {};
  // `Server Stream` Call
  rpc ServerToClient (Chunk) returns (stream Chunk) {};
  // `Duplex` Call
  rpc DuplexTransfer (stream Chunk) returns (stream Chunk) {};
}
```
