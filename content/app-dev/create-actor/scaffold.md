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
cargo generate --git https://github.com/wasmcloud/new-actor-template --branch main
```

You should see output similar to the following:

```shell
cargo generate --git https://github.com/wasmcloud/new-actor-template
 Project Name: example
 Creating project called `example`...
 Done! New project created /home/test/wasmcloud/rust/example
```

This creates an empty actor with the only function being the initialisation of the actor and a default, mandatory health checker.
Let's take a look at the created `src/lib.rs` file:

```rust
extern crate wapc_guest as guest;
use wasmcloud_actor_core as actor;

use guest::prelude::*;

#[actor::init]
fn init() {
    // Register your message handlers here
}
```

There are two external dependences here: `wapc_guest` and `wasmcloud_actor_core`.
`wapc_guest` provides the ability to receive function calls according to the [waPC](https://wapc.io/) specification. 
`wasmcloud_actor_core` contains the data types required by all actors, namely the health check request and health check response, and CapabilityConfiguration, a struct used by capability providers to receive link data for an actor.

The macro `actor::init` will create a default health check message handler.  All actors
must respond to health checks whenever the host runtime requests them. This is how the host runtime 
can tell if an actor that may appear to be running is truly healthy.
Let's move on to defining some new message handlers.

