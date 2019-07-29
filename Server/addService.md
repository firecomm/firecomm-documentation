# Server.addService( )

### parameters

1. #### SERVICE_DEFINITION `object` // Service as it is named on your `.proto` file. **Is a property on the built package.**
2. #### RPC_METHODS_OBJECT *object* // maps each `RPC_METHOD`	to it's `HANDLER_FUNCTION` or `MIDDLEWARE_STACK`.
   1. ##### RPC_METHOD `property` // must match each `rpc Method` named in the `.proto`
      	1. ##### HANDLER_FUNCTION `value` // function to handle the `rpc Method`
	        ##### *OR*
      	2. ##### MIDDLEWARE_STACK `value` // array of *functions* to handle the `rpc Method`. The main `HANDER_FUNCTION` to be run must be last in the array.


   #### returns `undefined`

### returns SERVER `object` // an instance of the Firecomm Server class

```javascript
const { Server } = require( "firecomm" );
const server = new Server( ERROR_HANDLER );
```

### Example

```javascript
const { Server } = require( "firecomm" );
const server = new Server(function( ERROR, CALL ) {
  if ( ERROR ) CALL.throw( ERROR );
});
```

1. ###### SERVICE_DEFINITION *object* // Service as it is named on your `.proto` file. **Is a property on the built package.**
2. ###### RPC_METHODS_OBJECT *object* // maps each `RPC_METHOD`	to it's `HANDLER_FUNCTION` or `MIDDLEWARE_STACK`.
	###### RPC_METHOD *property* // must match each `rpc Method` named in the `.proto`
	###### HANDLER_FUNCTION *value* // function to handle the `rpc Method`
	###### *OR*
	###### MIDDLEWARE_STACK *value* // array of *functions* to handle the `rpc Method`. The main `HANDER_FUNCTION` to be run must be last in the array.
```javascript
const { Server } = require( 'firecomm' );
const server = new Server();
server.addService( SERVICE, RPC_METHODS_OBJECT );
```