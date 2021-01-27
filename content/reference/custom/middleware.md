---
title: "Middleware Plugins"
date: 2018-12-29T11:02:05+06:00
weight: 4
draft: false
---

wasmCloud **_middleware plugins_** are used to allow code you write to be invoked during _every single invocation_. An invocation is a call between two entities within wasmCloud, and so can include the following use cases:

* Actor -> Actor
* Actor -> Capability Provider
* Capability Provider -> Actor

**Note** that capability providers are forbidden from talking directly to each other for a number of reasons, not the least of which is security and the prevention of privilege escalation attacks.

All wasmCloud middleware must conform to the `Middleware` trait, which is found in the [wasmcloud-host](https://crates.io/crates/wasmcloud-host) crate. The required functions of this trait are:

* `actor_pre_invoke(&Invocation) -> Result<()>`

Invoked prior to an actor receiving an invocation from _any source_. Return `Ok` to indicate successful processing.

* `actor_post_invoke(InvocationResponse) -> Result<InvocationResponse>`

Invoked after an actor has handled an invocation. Your code is given a chance to return a new invocation response, note that the original response is consumed (_moved_) in this call.

* `capability_pre_invoke(&Invocation) -> Result<()>`

Invoked prior to a capability provider receiving an invocation from an actor.

* `capability_post_invoke(InvocationResponse) -> Result<InvocationResponse>`

Invoked after a capability provider has processed an invocation, giving your code a chance to alter or replace the original invocation response.

All of these middleware calls are _chain-breaking_ calls. If your code ever returns anything other than an `Ok<T>`, then the middleware chain will be broken and the original initiator of the call will be given the error result.

⚠️ **NOTE** Middleware chains are processed in the order in which they were added to the `HostBuilder`. So if you require that middleware processing follow a specific order, you _must_ ensure that your plugins are added to the host builder in the right order.
