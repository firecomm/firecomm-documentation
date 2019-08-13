# build( )
```javascript
const { build } = require( "firecomm" );
const path = require( "path" )
const PROTO_PATH = path.join( __dirname, './example.proto' )

const package = build( PROTO_PATH );

module.exports = package;
```

### parameters

1. #### PROTO_PATH `string` // absolute path to the .proto file to be transpiled into Node.js

2. #### [ *optional* ] CONFIG_OBJECT `object` // object for configuring transpiling data types. Takes up to nine optional properties:
    #### options

    1. ##### keepCase: `boolean` // allows rpc methods to have strict camelCasing or PascalCasing within the transpiled `package`
    2. ##### longs: `String` or `Number` // `int64`s, `uint64`s, `sint64`s, and `fixed64`s will be coerced into a `String` or a `Number`.
    3. ##### enum: `String` // Java-like `enumerable` will be accessible from an Integer or from the Enumerable option written as a `String`.
    4. ##### bytes: `Array` or `String` // gRPC `byteStrings` will be coerced into a `String` or an `Array`. 
    5. ##### defaults: `boolean` // allows the setting of default values for `message` types.
    6. ##### arrays: `boolean` // allows the coercion of `repeated` `message` types into `Array`s.
    7. ##### objects: `boolean` // allows the coercion of nested `message` types into `Object`s. 
    8. ##### oneofs: `boolean` // allows the setting of unique `message` types.
    9. ##### includeDirs: `array` or `string` // an absolute path to an additional `.proto` file, or an array of absolute paths to additional `.proto` files to be combined in your returned package.

### returns `package` `object`

> Note: whatever names you gave to the `package`, `service`s, `rpc Method`s, and `message`s will live on the returned `package` and will define the syntax of `Server.addService()`, `Stub`, and the rpc methods that live on each `Stub`.

### example with `CONFIG_OBJECT`

```javascript
const { build } = require( "firecomm" );
const path = require( "path" )
const PROTO_PATH = path.join( __dirname, './example.proto' )

const package = build( PROTO_PATH, {
  keepCase: true,
  longs: Number,
  enums: String,
  bytes: String,
  defaults: true,
  arrays: true,
  objects: true,
  oneofs: true,
  includeDirs: [ path.join( __dirname, './another.proto', path.join( __dirname, './customHealthCheck.proto ) ],
} );

module.exports = package;
```

> Note: the returned `package` must be exported so that it can be used elsewhere in the application for your `Server`, client `Stubs` or your client/`Server` hybrid with `Stubs`.