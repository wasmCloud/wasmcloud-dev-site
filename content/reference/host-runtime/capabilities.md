---
title: "Capabilities"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

A **_capability_** is an abstraction or representation of a non-functional requirement; some functionality required by your actor that is not considered part of the core business logic. As you write an actor that exposes some data over a RESTful endpoint, the HTTP server is not part of your business logic, it is a service used by your actor--a capability.

In wasmCloud, _capability providers_ are dynamic libraries that implement a _capability contract_. A capability contract is a unique name that identifies the interface or abstraction. By convention, these capability contract IDs are prefixed by a vendor ID (the vendor of the _contract_, not necessarily the specific _implementation_). For example, the following is a list of the first-party capability contract IDs supported by default wasmCloud providers:

* wasmcloud:http_server
* wasmcloud:http_client
* wasmcloud:messaging
* wasmcloud:telnet
* wasmcloud:keyvalue
* wasmcloud:graphdb
* wasmcloud:logging
* wasmcloud:blobstore

In our case, all of the contracts created by the wasmCloud team are prefixed with the `wasmcloud` prefix. This does not mean that other organizations cannot provide an implementation of a wasmCloud contract. On the contrary, we have multiple implementations of wasmCloud contracts for things like an S3 blob store, multiple key-value providers, etc.

### Capability Provider FFI

In the current version of wasmCloud, capability providers must be compiled into what are called "shared objects". These take the form of `.so` files on Linux, `.dylib` files on macOS, and `.dll` files on Windows. You can implement a capability provider in any language, so long as the target output can be one of these shared object files and it conforms to the [FFI](https://en.wikipedia.org/wiki/Foreign_function_interface) requirements for a capability provider.

The wasmCloud host runtime will instantiate a new capability provider by invoking the following function:

```rust
pub extern "C" fn __capability_provider_create() -> *mut CapabilityProvider 
```

where `CapabilityProvider` is a trait implemented in Rust. If you're writing a capability provider in another language other than Rust, you can feel free to return a pointer to that object, provided the associated function calls match the signature required by the trait.

When implementing your own capability provider in Rust, you can use the [wasmcloud-codec](https://github.com/wasmCloud/wascc-codec) crate's `capability_provider!` macro to provide a developer-friendly way of implementing the FFI function.

#### Capability Provider Trait Requirements

The following is the Rust definition for the `CapabilityProvider` trait:

```rust
pub trait CapabilityProvider: CloneProvider + Send + Sync {
    /// This function will be called on the provider when the host runtime is ready and has configured a dispatcher. This function is only ever
    /// called _once_ for a capability provider, regardless of the number of actors being managed in the host
    fn configure_dispatch(
        &self,
        dispatcher: Box<dyn Dispatcher>,
    ) -> Result<(), Box<dyn Error + Send + Sync>>;
    /// Invoked when an actor has requested that a provider perform a given operation
    fn handle_call(
        &self,
        actor: &str,
        op: &str,
        msg: &[u8],
    ) -> Result<Vec<u8>, Box<dyn Error + Send + Sync>>;
    /// This function is called to let the capability provider know that it is being removed
    /// from the host runtime. This gives the provider an opportunity to clean up any
    /// resources and stop any running threads
    fn stop(&self);
}
```

All capability providers **MUST** respond to the following operations (invoked via `handle_call`):

* **BindActor** - Called with a serialized version of the `CapabilityConfiguration` struct when an actor is being _linked_ to a capability provider. This operation _must_ be idempotent, and so must simply return an `Ok` with an empty vector as the response if multiple linking attempts are performed for the same actor.
* **RemoveActor** - The opposite of linking an actor to a provider, this is called by the host runtime to indicate that a given actor is no longer actively communicating with (and using the resources of) the provider. This also must be an idempotent operation, and providers must happily return an empty byte vector for attempts to remove non-existent or already-removed actors.
* **HealthRequest** - The wasmCloud host runtime will periodically (providers are not privy to the frequency) poll a provider to check to see if it is healthy. If it responds without error, but indicates that it is unhealthy, it will be stopped and removed from the runtime. Return an `Ok` with a `HealthResponse` struct to indicate a properly running provider.

⚠️ You must also be sure that you do _nothing_ in the `stop` function call that might panic, which includes writing to `STDOUT` when that facility might be unavailable. In most cases, you probably want to simply do nothing in this function, because per-actor resources should be cleaned up when handling the `RemoveActor` operation.

### Building a Provider

Building a provider is most easily done in Rust. For a hands-on tutorial and walk-through guide, check out our [creating a capability provider](/app-dev/create-provider) guide.

### WASI-Based Capability Providers 🔮

In the (hopefully) near future, the [WASI](https://wasi.dev/) specification will support regular socket-based networking, and multiple low-level WebAssembly engines (such as [wasm3](https://github.com/wasm3/wasm3) and [wasmtime](https://github.com/bytecodealliance/wasmtime)) will support WASI-based networking functionality. When this functionality is available, reliable, and well-tested in the WASI spec and supporting engines, then wasmCloud will automatically support the ability to create capability providers as portable, "privileged" WebAssembly modules that use WASI to provide networking services to their linked actors.

This section of the documentation will change when that functionality is ready to use.
