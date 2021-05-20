---
title: "Building a Host from Spec"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

In the preceding section of the documentation, we talked about how to create your own custom hosts that utilize
the existing wasmcloud-host Rust crate. In this section, we'll talk about the specification and requirements for wasmCloud
hosts written from scratch either for internal use or community contribution (e.g. a custom host for an `ESP32` microcontroller, etc).

If you want to make your own wasmCloud host, you're likely doing so because you want to add a device or operating system that we don't support to the ecosystem, one that will be able to co-exist with first-party wasmCloud hosts and interact with the lattice. The following requirements for hosts will give you the recipe you need to create your own host.

### Host Requirements (Internal)

Internally, the host must perform the following activities:

* Periodically send a [HealthRequest](https://github.com/wasmCloud/actor-interfaces/blob/main/actor-core/core.widl#L24) message to each running actor _and_ capability provider. These messages are to remain internal and are NOT to be transmitted across a lattice. It is up to the host as to what it does with the information it gleans from these health checks, but health checks _must_ be performed.
* Properly dispatch the `BindActor` message to providers  
* Properly dispatch the `RemoveActor` message to providers
* Handle _local_ dispatch between actors and providers if the host supports internal providers
* Handle _local_ dispatch between actors and actors
* Expose some form of API that can be used to start and stop actors
  * Actors must communicate with the host using the [waPC](/reference/wapc) specification. Custom hosts do not have to re-use existing waPC Rust crates, they only have to ensure that the host exposes the same waPC functions as the first-party wasmCloud host.
  * The host does not need to use `widl` or any other waPC code generation facilities, so long as the host is able to use _messagepack_ for the serialization format for messages link establishing a link definition (`BindActor`) and health checks.
* _Optionally_ expose an API that is used to start and stop providers. Custom wasmCloud hosts do not need to support internal capability providers.
* Defer to lattice-based RPC during dispatch if no local targets for invocations are discovered.
* Validate the JWT embedded in actors and in provider archives, including "not-before", "expires", and the actor claims (prevent actors from communicating with provider contracts not listed in the claims).
* Generate a new `nkey` pair for the host upon startup. This is used for the public key to identify the host and for the host to sign invocations it sends across the lattice.

### Host Requirements (External)

Externally, the host must adhere to the following requirements:

* Subscribe to the appropriate `lattice` [subjects](https://github.com/wasmCloud/wasmCloud/blob/main/crates/wasmcloud-control-interface/src/broker.rs) for:
  * RPC - each actor must subscribe to its appropriate lattice RPC subject
  * Control Interface - the host must subscribe to the control interface subject(s) and respond in accordance with the [wasmcloud-control-interface](https://github.com/wasmCloud/wasmCloud/tree/main/crates/wasmcloud-control-interface) crate's client-side expectations.
  * Events - What the custom host does with the events is up to the host, but the host _must_ emit (publish) all [expected events](https://github.com/wasmCloud/wasmCloud/blob/main/crates/wasmcloud-control-interface/src/events.rs#L27).
* Ensure that the `Invocation` that is sent via RPC contains the a properly signed JWT with the proper invocation hash (anti-forgery token implementation). First-party wasmCloud hosts will rejectr invocations that do not include these.
* Properly configure actors and providers upon receipt of the "advertise link definition" message on the control interface.
* Publish the "advertise link definition" every time a link definition changes or is created. This is _mandatory_, even if the host thinks all other hosts are aware of the change.
* Publish claims, OCI references, and call aliases as they are created or updated (this is a _grow-only_ cache) to the distributed cache if at least one host in the lattice is using the NATs-replicated lattice cache provider.
