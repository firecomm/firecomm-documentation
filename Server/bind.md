# Server.bind( )

Invoke this method to connect your server instance to a channel. Allows for the creation of secure and insecure channels.

```javascript
const { Server } = require( 'firecomm' );
const server = new Server();
server.bind("0.0.0.0:8080")
```

### parameters

1. #### SOCKET `string` // Socket in the form of `IP`:`PORT` for Firecomm `Server` class to listen on.
2. ####  [ *optional* ] Config`object` // if no config is supplied, an insecure channel will be created (no TLS handshake required and traffic unencrypted, to create a secure connection, supply the paths to an RSA private key and certificate

   #### options

   1. ##### privateKey: `string` // path to your RSA private key
   2. ##### certificate: `string` // path to your SSL Certificate


### returns `undefined`

### example

```javascript
const { Server } = require( "firecomm" );

const privateKey = require( "./certificate.crt" )
const certificate = require( "./private_key.key" )

const server = new Server();

server.bind( '0.0.0.0:3000', {certificate, privateKey } )

```