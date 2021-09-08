---
title: "Create the Scaffold"
date: 2018-12-29T11:02:05+06:00
weight: 3
draft: false
---

Creating the scaffold for a new actor in Rust is easy. We will create an actor that accepts an http request and responds with "Hello World". To create a new actor project, change to the directory where you want the project to be created. The following command will create a new project named `hello` in a subdirectory. You may choose a different project name if you wish. If you choose a different project name, the name of the new subdirectory will reflect the project name, and the project name will be be incorporated into some of the symbols in the generated code.

```
wash new actor hello
```

Let's change into the newly-created folder `hello` and take a look at the generated project. The file `src/lib.rs` should look something like this:

```rust
use wasmbus_rpc::actor::prelude::*;
use wasmcloud_interface_httpserver::{HttpRequest, HttpResponse, HttpServer, HttpServerReceiver};

#[derive(Debug, Default, Actor, HealthResponder)]
#[services(Actor, HttpServer)]
struct HelloActor {}

/// Implementation of HttpServer trait methods
#[async_trait]
impl HttpServer for HelloActor {

    /// Returns a greeting, "Hello World", in the response body.
    /// If the request contains a query parameter 'name=NAME', the
    /// response is changed to "Hello NAME"
    async fn handle_request(
        &self,
        _ctx: &Context,
        req: &HttpRequest,
    ) -> std::result::Result<HttpResponse, RpcError> {
        let text = form_urlencoded::parse(req.query_string.as_bytes())
            .find(|(n, _)| n == "name")
            .map(|(_, v)| v.to_string())
            .unwrap_or_else(|| "World".to_string());

        Ok(HttpResponse {
            body: format!("Hello {}", text).as_bytes().to_vec(),
            ..Default::default()
        })
    }
}

```

Note the two lines near the top of the source code file:

```rust
use wasmbus_rpc::actor::prelude::*;
use wasmcloud_interface_httpserver::{HttpRequest, HttpResponse, HttpServer, HttpServerReceiver};
```
This shows us that we're using a core wasmCloud crate called `wasmbus_rpc` and we've also declared a dependency on the `wasmcloud_interface_httpserver` crate. By convention, all first-party wasmCloud interfaces begin with `wasmcloud_interface`.

Just below that, you'll see:

```
#[derive(Debug, Default, Actor, HealthResponder)]
#[services(Actor, HttpServer)]
struct HelloActor {}
```

`HelloActor` is the name of your actor - if you chose a different project name, the actor name will include the project name.

The two lines above the actor name invoke Rust macros that generate - at compile time - nearly all of the scaffolding needed to build an actor. The `HealthResponder` term generates a function that automatically responds to health check queries from the wasmCloud host. The `#[services(...)]` line declares the services ('traits', in Rust) that your actor implements, and generates message handling code for those interfaces. All actors implement the `Actor` interface. The `HttpServer` entry declares that the actor will also implement that interface, and requires an implementation of that trait's method: `handle_request`.

Within the `handle_request` method, the actor receives the request, and returns an HttpResponse, which is sent back to the http client. We'll look into the details of that method, and customize it, shortly.

If you use an IDE that comes with code completion and hover-tooltips, you'll be able to see documentation and get easy, strongly-typed guidance as you develop code to interact with the wasmCloud interfaces.

### Something's Missing

Before we get into modifying the scaffolding to create the rest of this actor, take a look at what's _not_ included in this code. This code returns an abstraction of an HTTP response, it is _not_ tightly coupled to any particular HTTP server. Further, you don't see the port number or server configuration options anywhere in the code. Finally, you can scale and compose this actor any way you see fit without ever having to recompile or redeploy it.

_This is the way development was meant to be_.

Pure business logic, with all of your non-functional requirements handled through loosely coupled abstractions by runtime-configurable hosts. _No boilerplate, no fuss_.