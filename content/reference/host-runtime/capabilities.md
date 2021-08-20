---
title: "Capabilities"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

A **_capability_** is an abstraction or representation of a non-functional requirement; some functionality required by your actor that is not considered part of the core business logic. As you write an actor that exposes some data over a RESTful endpoint, the HTTP server and the database are not parts of your business logic, they are services used by your actor--capabilities.

In wasmCloud, _capability providers_ are dynamic libraries that implement a _capability contract_. A capability contract is a unique name that identifies the interface or abstraction. By convention, these capability contract IDs are prefixed by a vendor ID (the vendor of the _contract_, not necessarily the specific _implementation_). For example, the following is a list of the first-party capability contract IDs supported by default wasmcloud providers:

* `wasmcloud:httpserver`
* `wasmcloud:httpclient`
* `wasmcloud:messaging`
* `wasmcloud:keyvalue`
* `wasmcloud:blobstore`

Additionally, the following capability providers are _built-in_, and while they still require security claims, they do not require link definitions:

* `wasmcloud:builtin:numbergen`
* `wasmcloud:builtin:logging`

In our case, all of the contracts created by the wasmCloud team are prefixed with the `wasmcloud` prefix. This does not mean that other organizations cannot provide an implementation of a wasmCloud contract. On the contrary, we have multiple implementations of wasmCloud contracts for things like an S3 blob store, multiple key-value providers, etc.

### Building a Provider

Building a provider can be done in any language, though most of our samples are in Rust or Go. For a hands-on tutorial and walk-through guide, check out our [creating a capability provider](/app-dev/create-provider) guide.

### WASI-Based Capability Providers 🔮

In the (hopefully) near future, the [WASI](https://wasi.dev/) specification will support regular socket-based networking, and multiple low-level WebAssembly engines (such as [wasm3](https://github.com/wasm3/wasm3) and [wasmtime](https://github.com/bytecodealliance/wasmtime)) will support WASI-based networking functionality. When this functionality is available, reliable, and well-tested in the WASI spec and supporting engines, then wasmCloud will automatically support the ability to create capability providers as portable, "privileged" WebAssembly modules that use WASI to provide networking services to their linked actors.

This section of the documentation will change when that functionality is ready to use.
