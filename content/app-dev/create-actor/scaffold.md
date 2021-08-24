---
title: "Create the Scaffold"
date: 2018-12-29T11:02:05+06:00
weight: 3
draft: false
---

Creating the scaffold for a new actor in Rust is very easy. To create a new actor project, simply issue the following `wash` command:

```
wash new actor
```

This will prompt you for the information needed to create a new project. Assuming you indicated that your new project was named `hello`, then you could now change into the `hello` directory and you will have a ready-to-build Rust project. 

The new actor created by the scaffolding is a "hello world" actor that responds to an HTTP request with a response containing a description of the request.

Let's take a look at the created `src/lib.rs` file:

```rust
TODO
```

Note the two lines at the top of the source code file:

```rust
use wasmbus_rpc::actor::prelude::*;
use wasmcloud_interface_httpserver::{HttpRequest, HttpResponse, HttpServer, HttpServerReceiver};
```
This shows us that we're using a core wasmCloud crate called `wasmbus_rpc` and we've also declared a dependency on the `wasmcloud_interface_httpserver` crate. By convention, all first-party wasmCloud interfaces begin with `wasmcloud_interface`.

Defining the business logic for your actor's message handlers is as simple as implementing the `HttpServer` trait on your actor. If you use an IDE that comes with hover tooltips and lookups, then you'll be able to get easy, strongly-typed guidance throughout the process.

### Something's Missing

Before we get into modifying the scaffolding to create the rest of this actor, take a look what what's _not_ included in this code. This code returns an abstraction of an HTTP response, it is _not_ tightly coupled to any particular HTTP server. Further, you don't see the port number or server configuration options anywhere in the code. Finally, you can scale and compose this actor any way you see fit without ever having to recompile or redeploy it.

_This is the way development was meant to be_.

Pure business logic, with all of your non-functional requirements handled through loosely coupled abstractions by runtime-configurable hosts. _No boilerplate, no fuss_.