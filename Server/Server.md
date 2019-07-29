# Server( )
```javascript
const { Server } = require( "firecomm" );
const server = new Server( ERROR_HANDLER );
```
### parameters

1. #### ERROR_HANDLER `function` // absolute path to the .proto file to be transpiled into Node.js

   #### parameters

   1. ##### ERROR`object` //
   2. ##### CALL `object` //

   #### returns `undefined`

### returns SERVER `object` // an instance of the Firecomm Server class

### example

```javascript
const { Server } = require( "firecomm" );
const server = new Server(function( ERROR, CALL ) {
  if ( ERROR ) CALL.throw( ERROR );
});
```
