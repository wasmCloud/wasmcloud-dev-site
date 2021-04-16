---
title: "Define Message Handlers"
date: 2018-12-29T11:02:05+06:00
weight: 4
draft: false
---

Our new actor already has one handler, the health check. Let's add a new handler for an HTTP request that will perform the actor equivalent of "hello world"--return an HTTP response with that as the body. To make it a little bit more practical, let's return the "hello world" string in a field of a JSON payload.

First, we'll need to add a reference to the HTTP server actor interface and JSON serialization to our `Cargo.toml` file:

```toml
serde_json = "1.0.59"
wasmcloud-actor-http-server = { version = "0.2.2", features = ["guest"]}
```

In this case we're using a git reference for the actor interface,
 but you'll want to use the [crates.io](https://crates.io/crates/actor-http-server) version for anything more than experimentation.

Now let's add the handler to our (Rust) actor (`src/lib.rs`):

```rust
extern crate wapc_guest as guest;
use wasmcloud_actor_core as actorcore;
use wasmcloud_actor_http_server as http;
use serde_json::json;

use guest::prelude::*;

#[actorcore::init]
pub fn init() {
  http::Handlers::register_handle_request(say_hello);
}

fn say_hello(_req: http::Request) -> HandlerResult<http::Response> {
  let result = json!({"greeting": "Hello world!" });
  Ok(http::Response::json(&result, 200, "OK"))
}
```

To make sure this actor is compiled and ready to run, we can simply run `make` in the project directory and `cargo` will compile it and generate a `.wasm` file and then the `wash` CLI will sign the module with a set of claims that includes access to the HTTP Server capability.
