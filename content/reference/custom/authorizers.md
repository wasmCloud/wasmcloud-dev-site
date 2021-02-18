---
title: "Authorizer Plugins"
date: 2020--1-26T11:02:05+06:00
weight: 3
draft: false
---

A wasmcloud _authorizer plugin_ is a Rust struct that implements the `Authorizer` trait. wasmcloud will **always** enforce that a given actor has the required capability attestation claims to invoke a capability provider. By default, wasmcloud allows actor-to-actor communications without restriction. If you wish to _further limit_ the authorization scope used by wasmcloud, then you can supply your own authorizer which will be added to the security checks performed by the host runtime.

⚠️ **NOTE** - You can only ever _further limit_ the scope of things granted access. Your authorizer plugin can never be used to _reduce security_ by increasing the number of ways to obtain positive authorization.

The `Authorizer` trait (which is found in the [wasmcloud-host](https://github.com/wasmcloud/wasmcloud/tree/main/crates/wasmcloud-host) crate), requires the following functions:

* `can_load(&Claims<Actor>) -> bool`

Your plugin will be given the claims of the actor that is about to be loaded. If your function returns false, the actor module will not be loaded.
* `can_invoke(&Claims<Actor>, &WasmCloudEntity, &str) -> bool`

This function will _only_ be invoked if the core wasmcloud host has already determined that an invocation is authorized. Your function can then be used to decide to reject that assertion by returning `false`, or returning `true` to allow the invocation to go through.

In a [release coming very soon](https://github.com/wasmcloud/wasmcloud/issues/72), the authorizer plugins will have access to the _claims_ of the target as well, allowing plugins to do things like perform allow-list checks against the issuer of the target, or require that both the source and the target of the invocation had the same issuer, etc.

⚠️ **NOTE** All first-party capability providers and example actors released by the wasmcloud team will be issued by the following account: 

```
ACOJJN6WUP4ODD75XEBKKTCCUJJCY5ZKQ56XVKYK4BEJWGVAOOQHZMCW
```
