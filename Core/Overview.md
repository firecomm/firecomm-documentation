# Core Concepts

As mentioned previously, Firecomm can be broken into serveral core pieces.

- `build` - defines the logic for Servers and Stub methods
  - accessible through a top-level constructor function
- `Server` - where the handlers for those methods live
  - accessible through a top-level constructor function
- `Stub` - Stubs initiate communication with the Server by invoking Server methods
  - accessible through a top-level constructor function
- Calls - Calls are the individual unit of communication for each method invocation
  - accessible Server-side as the primary argument for method handlers and middleware functions
  - accessible as the output of a Stub-side, invoked gRPC method

