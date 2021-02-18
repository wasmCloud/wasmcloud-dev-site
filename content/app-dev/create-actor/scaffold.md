---
title: "Create the Scaffold"
date: 2018-12-29T11:02:05+06:00
weight: 3
draft: false
---

Creating the scaffold for a new actor in Rust is very easy. First, you'll need to make sure you have the [generate](https://crates.io/crates/cargo-generate) cargo plugin:

```
cargo install cargo-generate
```

Once you've installed `cargo-generate`, then we can create a new actor using wasmcloud's [new actor template](https://github.com/wasmcloud/new-actor-template):

```
cargo generate --git https://github.com/wasmcloud/new-actor-template
```

You should see output similar to the following:

```shell
cargo generate --git https://github.com/wasmcloud/new-actor-template
 Project Name: example
 Creating project called `example`...
 Done! New project created /home/test/wasmcloud/rust/example
```

Your new project should be ready to build. Your actor scaffolding comes equipped with a mandatory message handler: the health check. All actors
must respond to health checks whenever the host runtime requests them. This is how the host runtime can tell if an actor that may appear to be running is truly healthy.

Let's take a look at the default `src/lib.rs` file:

```rust
extern crate wapc_guest as guest;
use actor_core as actorcore;

use guest::prelude::*;

#[no_mangle]
pub fn wapc_init() {
    actorcore::Handlers::register_health_request(health);
}

fn health(_h: actorcore::HealthCheckRequest) -> 
    HandlerResult<actorcore::HealthCheckResponse> {
        Ok(actorcore::HealthCheckResponse::healthy())
}
```

This actor has a single message handler that always indicates the actor is healthy. Let's move on to defining some new message handlers.
