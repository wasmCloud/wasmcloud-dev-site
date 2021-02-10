---
title: "Creating Custom Hosts"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

wasmCloud releases all include a default set of binaries, including a `wasmCloud` binary that is available for multiple operating systems and processor architectures. However, you may want to create your own fit-for-purpose host. For this purpose, [wasmCloud](https://github.com/wasmCloud/wasmcloud) is also available for use as a [Rust crate](https://crates.io/crates/wasmcloud-host), allowing you to declare a dependency and create your own host.

### Declaring a Dependency on wasmCloud

Declaring a dependency on wasmCloud is just a matter of adding a reference to it in your `Cargo.toml` file:

```toml
wasmcloud-host = "0.15.0"
```

### Choosing a Low-Level WebAssembly Engine

By default, the wasmCloud crate will choose its default underlying WebAssembly engine, [wasm3](https://github.com/wasm3/wasm3). This is chosen as a default because it is the _all-around_ fastest engine, and since it does interpretation rather than JIT, it supports a larger number of potential targets. However, if you wish to change this (or make your choice explicit), then you can choose the supporting WebAssembly execution engine through a _feature flag_ in the `Cargo.toml` file.

Choose **wasm3**:

```toml
wasmcloud-host = { version = "0.15.0", features = ["wasm3"] }
```

Choose **[wasmtime](https://wasmtime.dev/)** (part of the [bytecode alliance](https://bytecodealliance.org/)):

```toml
wasmcloud-host = { version = "0.15.0", features = ["wasmtime"] }
```

### Manipulating the Custom Host

If there are capability providers that you want to load automatically at start-up, built-in actors you want to always start, or special configurations (including custom middleware or [authorizers](../authorizer)), then you can simply invoke the appropriate functions on the `Host` class, as described in the [Rust documentation](https://docs.rs/wasmcloud-host).
