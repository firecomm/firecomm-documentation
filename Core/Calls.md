# Calls

Call objects are the primary unit of communication within gRPC. 

> NOTE: One call is generated for each method invocation.

There are two types of call objects you will encounter in Firecomm.
#### 1.  Server-Side Calls
   - Passed as the sole argument into each middleware function / method handler
#### 2. Stub-Side Calls
  - Returned from invoking a gRPC method on a stub

Firecomm has a unified syntax which makes each of these easy to interact with.

## Server Calls


```javascript

```

## Stub Calls


# Call Types

> NOTE: There are eight types of calls in the gRPC ecosystem. 

Below are the four main gRPC method types. 

|                      | Client Unary        | Client Streaming    |
| -------------------- | ------------------- | ------------------- |
| **Server Unary**     | UnaryCall           | ClientStreamingCall |
| **Server Streaming** | ServerStreamingCall | DuplexCall          |

But while there are four types of methods here, there are actually two variations of calls to accompany each method in the quadrant.
1. Server-Side Call
2. Stub-Side Call


