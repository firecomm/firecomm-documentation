Module: src/client_interceptors
===============================

Client Interceptors

This module describes the interceptor framework for clients. An interceptor is a function which takes an options object and a nextCall function and returns an InterceptingCall:
```
var interceptor = function(options, nextCall) {
  return new InterceptingCall(nextCall(options));
}
```
The interceptor function must return an InterceptingCall object. Returning `new InterceptingCall(nextCall(options))`will satisfy the contract (but provide no interceptor functionality). `nextCall` is a function which will generate the next interceptor in the chain.

To implement interceptor functionality, create a requester and pass it to the InterceptingCall constructor:

`return new InterceptingCall(nextCall(options), requester);`

A requester is a POJO with zero or more of the following methods:

`start(metadata, listener, next)`

-   To continue, call next(metadata, listener). Listeners are described
-   below.

`sendMessage(message, next)`

-   To continue, call next(message).

`halfClose(next)`

-   To continue, call next().

`cancel(message, next)`

-   To continue, call next().

A listener is a POJO with one or more of the following methods:

`onReceiveMetadata(metadata, next)`

-   To continue, call next(metadata)

`onReceiveMessage(message, next)`

-   To continue, call next(message)

`onReceiveStatus(status, next)`

-   To continue, call next(status)

A listener is provided by the requester's `start` method. The provided listener implements all the inbound interceptor methods, which can be called to short-circuit the gRPC call.

Three usage patterns are supported for listeners: 1) Pass the listener along without modification: `next(metadata, listener)`. In this case the interceptor declines to intercept any inbound operations. 2) Create a new listener with one or more inbound interceptor methods and pass it to `next`. In this case the interceptor will fire on the inbound operations implemented in the new listener. 3) Make direct inbound calls to the provided listener's methods. This short-circuits the interceptor stack.

Do not modify the listener passed in. Either pass it along unmodified, ignore it, or call methods on it to short-circuit the call.

To intercept errors, implement the `onReceiveStatus` method and test for `status.code !== grpc.status.OK`.

To intercept trailers, examine `status.metadata` in the `onReceiveStatus` method.

This is a trivial implementation of all interceptor methods: var interceptor = function(options, nextCall) { return new InterceptingCall(nextCall(options), { start: function(metadata, listener, next) { next(metadata, { onReceiveMetadata: function (metadata, next) { next(metadata); }, onReceiveMessage: function (message, next) { next(message); }, onReceiveStatus: function (status, next) { next(status); }, }); }, sendMessage: function(message, next) { next(message); }, halfClose: function(next) { next(); }, cancel: function(message, next) { next(); } }); };

This is an interceptor with a single method: var interceptor = function(options, nextCall) { return new InterceptingCall(nextCall(options), { sendMessage: function(message, next) { next(message); } }); };

Builders are provided for convenience: StatusBuilder, ListenerBuilder, and RequesterBuilder

gRPC client operations use this mapping to interceptor methods:

grpc.opType.SEND_INITIAL_METADATA -> start grpc.opType.SEND_MESSAGE -> sendMessage grpc.opType.SEND_CLOSE_FROM_CLIENT -> halfClose grpc.opType.RECV_INITIAL_METADATA -> onReceiveMetadata grpc.opType.RECV_MESSAGE -> onReceiveMessage grpc.opType.RECV_STATUS_ON_CLIENT -> onReceiveStatus

### Methods

* * * * *

#### inner getCall(channel, path [, options])

Get a call object built with the provided options.

##### Parameters:

| Name | Type | Argument | Description |
| --- | --- | --- | --- |
| `channel` | grpc.Channel|  |  |
| `path` | string |  |  |
| `options` | grpc.Client~CallOptions | optional | Options object.

* * * * *

#### inner getInterceptingCall(method_definition, options, interceptors, channel, responder)

##### Parameters:

| Name | Type | Description |
| --- | --- | --- |
| `method_definition` | grpc~MethodDefinition |  |
| `options` | grpc.Client~CallOptions |  |
| `interceptors` | Array.Interceptor |  |
| `channel` | grpc.Channel |  |
| `responder` | function | EventEmitter |  |
* * * * *

#### inner getLastListener(method_definition, emitter [, callback])

Creates the last listener in an interceptor stack.

##### Parameters:

| Name | Type | Argument | Description |
| --- | --- | --- | --- |
| `method_definition` | grpc~MethodDefinition |  |  |
| `emitter` | EventEmitter |  |  |
| `callback` | function | optional |  |
##### Returns:

Type

grpc~Listener

* * * * *

#### inner resolveInterceptorProviders(providers, method_definition)

Transforms a list of interceptor providers into interceptors.

##### Parameters:

| Name | Type | Description |
| --- | --- | --- |
| `providers` | Array.InterceptorProvider |  |
| `method_definition` | grpc~MethodDefinition |  |
##### Returns:

Type

null | Array.Interceptor