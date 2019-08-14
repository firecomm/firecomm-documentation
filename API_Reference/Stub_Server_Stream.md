# Stub Server-Stream Request
Object for sending **one** stream-starting RPC Method **request** and listening for **any number** of RPC Method **responses**.
| Returned from          | Type   | Peer         | Description                                                                |
|------------------------|--------|--------------|----------------------------------------------------------------------------|
| `Stub.<RPCmethodName>()` | Object | Server-Stream | `<RPCmethodName>` defined without `stream` on request and with `stream` on response in `proto`. Peer is defined by methodName at Server | 

## Methods
### `.send(message)`

Emits a `'data'` event and sends `message` to peer.

alias:
> `.write(message)`

parameters:
| Name    | Type   | Description                                                    |
|---------|--------|----------------------------------------------------------------|
| message | Object | Properties should match the request `message` defined in the `proto` |
returns `Stub Server-Stream Request` to chain Methods

### `.catch(callback)`
Listener for `'error'` event from peer.

alias:
> `.on('error', callback)`

parameters:
| Name     | Type     | Parameter | Description                                   |
|----------|----------|-----------|-----------------------------------------------|
| callback(error) | Function | error     | Peer's thrown `error` is passed into callback |
returns `Stub Server-Stream Request` to chain Methods

### `.on(event, callback)`
Listener for `'data'`, `error`, `'metadata'`, or `'status'` event from peer.

parameters:
| Name     | Type/Options | Description                                                            |
|----------|--------------|------------------------------------------------------------------------|
| event    | String       | Event to listen for from peer.                                         |
|          | 'data'       | Listens for peer response. Callback gets passed `Message`.              |
|          | 'error'      | Listens for peer thrown error. Callback gets passed `Error`.            |
|          | 'metadata'   | Listens for Metadata object from peer. Callback gets passed `Metadata`. |
|          | 'status'     | Listens for change in connection status. Callback gets passed `Status`. |
| callback | Function     | Is passed `Message`, `Error`, `Metadata`, `Status` based on event.     |
returns `Stub Server-Stream Request` to chain Methods

### `.cancel()`
Non-chainable method that cancels ongoing connection. Results in the call ending with a CANCELLED status, unless it has already ended with some other status.

alias:

> `.throw()`


returns `undefined`

### `.getPeer()`
Non-chainable method that returns peer's URI.

returns `string` URI of peer