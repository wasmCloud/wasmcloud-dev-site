---
title: "Using Standard Capabilities"
date: 2018-12-29T11:02:05+06:00
weight: 3
draft: false
---

Using existing capabilities from within an actor is quick and easy. If an actor interface exists for the type of capability _contract_ you're looking to use, then it's just a matter of declaring a reference to it from inside your code.

### Find an Actor Interface

If you're looking to see which types of supported, first-party capability provider contracts are available, you'll want to take a look at the [actor interfaces](https://github.com/wasmcloud/actor-interfaces) repository. Here you will find links to documentation as well as a list of which language-specific providers are available and links to multiple implementations of the same contract (_file system_ and _S3_ are both available implementations of `wasmcloud:blobstore`, for example).

### Add the Actor Interface as a Dependency

It should also be very easy to simply declare a dependency on the actor interface from within your actor project. Remember that you're taking a dependency on the _abstract contract_, and not the concrete implementation. You might be declaring a dependency on the `wasmcloud:keyvalue` contract, and not on Redis or Cassandra.

As an example, here's what it looks like to add a dependency on the `wasmcloud:graphdb` actor interface in your `Cargo.toml`:

```rust
actor-graphdb = "0.1.0"
```

And then you can import the graph database actor interface code in your `src/lib.rs` file as shown:

```rust
use actor_graphdb as graph;
use graph::*;
```

### Start and Link your Actors

We've covered starting and linking actors in a few places throughout the documentation. Refer to the [Run the Actor](/app-dev/create-actor/run) section of the application development guide for more information.
