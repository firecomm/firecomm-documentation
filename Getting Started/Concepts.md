# Concepts

As in ordinary RESTful communication, there are computers who make the request (Clients, called "Stubs" in gRPC) and computers who handle the requests (Servers)

The basic framework operates around gRPC's `Servers` and `Stubs`. Everything found in the library assists developers with either `Servers` or `Stubs`. 

Such as:
- creating the logical skeleton for Server and Stub setup (build)
- configuring Servers and Stubs (Server constructor and Stub constructor)
- adding handlers, middleware, and logic to Servers (Server.addService())
- facilitating communication between Servers and Stubs (Stub methods and Calls)