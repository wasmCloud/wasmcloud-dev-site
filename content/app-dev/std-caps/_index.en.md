---
title: "Using Standard Capabilities"
date: 2018-12-29T11:02:05+06:00
weight: 3
draft: false
---

Using existing capabilities from within an actor is quick and easy. If an actor interface exists for the type of capability _contract_ you're looking to use, then it's just a matter of declaring a reference to it from inside your code. Remember that your actors depend only on the _interface_, the _implementation_ of a contract is entirely up to the provider. For instance, once your actor is designed to work against the `wasmcloud:keyvalue` contract, it automatically works with _any_ provider that implements that contract.

### Find an Actor Interface

If you're looking to see which types of supported, first-party capability provider contracts are available, you'll want to take a look at the [interfaces](https://github.com/wasmCloud/interfaces/) repository. Here you will find links to documentation as well as a list of which language-specific interfaces are available and links to multiple implementations of the same contract (e.g. _file system_ and _S3_ are both available implementations of `wasmcloud:blobstore`).

### Add the Actor Interface as a Dependency

It should also be very easy to simply declare a dependency on the actor interface from within your actor project. Remember that you're taking a dependency on the _abstract contract_, and not the concrete implementation. You might be declaring a dependency on the `wasmcloud:keyvalue` contract, and not on Redis or Cassandra.

By convention, all of our first-party actor interface crates start with the prefix `wasmcloud-interface` and should be easy to find on [crates.io](https://crates.io/search?page=1&per_page=10&q=wasmcloud-interface).

As an example, here's what it looks like to add a dependency on the **key-value** actor interface in your `Cargo.toml`:

```rust
wasmcloud-interface-keyvalue = "0.2.5"
```

And then you can import the key-value actor interface code in your `src/lib.rs` file as shown:

```rust
use wasmcloud_interface_keyvalue::{IncrementRequest, KeyValue, KeyValueSender};
```

You can also use the wildcard `*` to import all of the types from the interface.

### Sign Your Actor

Once you have the compiled actor (`.wasm`) module that depends on a capability provider, it needs to be signed to be allowed to use that provider. This is done by adding the appropriate flag to the `wash claims sign` command. The command `wash claims sign --help` will list the standard options and the flags needed for the standard capabilities. If you're using our standard scaffolding, you can simply change the corresponding line in the `Makefile` to reflect the providers you are using:

```make
CLAIMS   = wasmcloud:httpserver
```

### Start and Link your Actors

We've covered starting and linking actors in a few places throughout the documentation. Refer to the [Run the Actor](/app-dev/create-actor/run) section of the application development guide for more information.
