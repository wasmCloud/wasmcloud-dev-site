---
title: "Creating Custom Hosts"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

wasmcloud releases all include a default set of binaries, including a `wasmcloud` binary that is available for multiple operating systems and processor architectures. However, you may want to create your own fit-for-purpose host. For this purpose, [wasmcloud](https://github.com/wasmcloud/wasmcloud) is also available for use as a [Rust crate](https://crates.io/crates/wasmcloud-host), allowing you to declare a dependency and create your own host.

### Declaring a Dependency on wasmcloud

Declaring a dependency on wasmcloud is just a matter of adding a reference to it in your `Cargo.toml` file:

```toml
wasmcloud-host = "0.18.0"
```

### Choosing a Low-Level WebAssembly Engine

By default, the wasmcloud crate will choose its default underlying WebAssembly engine, [wasm3](https://github.com/wasm3/wasm3). This is chosen as a default because it is the _all-around_ fastest engine, and since it does interpretation rather than JIT, it supports a larger number of potential targets. However, if you wish to change this (or make your choice explicit), then you can choose the supporting WebAssembly execution engine through a _feature flag_ in the `Cargo.toml` file.

Choose **wasm3**:

```toml
wasmcloud-host = { version = "0.18.0", features = ["wasm3"], default-features = false }
```

Choose **[wasmtime](https://wasmtime.dev/)** (part of the [bytecode alliance](https://bytecodealliance.org/)):

```toml
wasmcloud-host = { version = "0.18.0", features = ["wasmtime"], default-features = false }
```

### Manipulating the Custom Host

If there are capability providers that you want to load automatically at start-up, built-in actors you want to always start, or special configurations (including custom middleware or [authorizers](../authorizer)), then you can simply invoke the appropriate functions on the `Host` class, as described in the [Rust documentation](https://docs.rs/wasmcloud-host).
