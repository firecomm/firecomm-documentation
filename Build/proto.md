# Building a `ProtoBuf` package in Node.js

Whenever spinning up a `Server` or a client with `Stub`s, the first step is to `build` a package out of a `.proto` file.

```javascript
const { build } = require('firecomm');

const package = build(PROTO_PATH);

module.exports = package;
```

## What is a `.proto` file?

Based on google's own documentation, we currently support `ProtoBuf`s written in `proto3` syntax.

> A `ProtoBuf` is an extensible mechanism shared across your entire API to define the `Services`, `RPC Methods`, and `Messages`. **Think of it like a schema for your API.**

