---
title: "Link Definitions"
date: 2018-12-29T11:02:05+06:00
weight: 8
draft: false
---

A **_link definition_** is a declared set of configuration values between a specific actor, identified by its public key, and a specific capability provider, identified by its contract ID, link name, and public key. At first glance it may seem like the "key" for this bi-directional relationship contains redundant information, but remember that there are 3 different consumers of this relationship: the host runtime, the actor, and the capability provider.

From the actor's point of view, code written using an actor interface _only_ refers to the combination of a contract ID and a link name, e.g. `wasmcloud:keyvalue` and `default`. This is because actors do not know about (nor should they ever) _which_ specific capability provider is responsible for supporting the contract on which they depend.

From the capability provider's point of view, it only ever dispatches messages to actors. When a link is established for a particular actor, the capability provider can remember that actor's public key and use it for subsequent dispatches. As a result, the only information a capability provider needs is the actor public key.

From the wasmCloud host runtime's point of view, it _must_ know the following information when establishing a link between capability provider and an actor:

* The actor's public key (which it can obtain indirectly via an [OCI reference](/platform-builder/oci)
* The contract ID (e.g. `wasmcloud:keyvalue` or `wasmcloud:httpserver`)
* The public key of the capability provider (which it can obtain indirectly from an OCI reference)
* The link name of the capability provider, which is actually a runtime attribute of a loaded instance of a capability provider. Any link name that is missing/unsupplied will be called `default`.

The key scenario that resulted in this multi-field "key" for link definitions is the following:

Assume that we have a **Redis** capability provider with the link name `default` running in the lattice. Now assume that we have an in-memory cache provider running with the link name `cache`. Finally, some other set of actors are also relying on a key-value provider, but this one is for **Consul** and its link name is `default`. If the link definition only contained contract ID and link name, then the host runtime would not have enough information to determine if an actor's request should be going to the **Redis** provider or the **Consul** provider. At its core, **wasmCloud** is basically a router. It needs enough information to establish routes between actors and capability providers.

It's for this reason that every link definition declaration must include (directly or indirectly via OCI) the provider's unique public key.

### Link Definitions at Runtime

Link definitions are declared, first-class citizens of a lattice network. This means that a link definition can be declared _before or after_ any of the pertinent parties of that link are running in the lattice. The wasmCloud host runtime will check every time an actor _or_ capability provider is started if an existing link definition pertains to them. If it does, the capability provider will be sent the "bind actor" message containing the previously stored link definition configuration values (basically a `HashMap`).

This has a few interesting implications. The first is that it makes for an incredibly low-friction developer experience. Order of operations does not matter - you can add actors, providers, and their corresponding link definitions to a host (or lattice) in any order and it "just works". The second is that all capability providers must treat the "bind actor" message as idempotent, and return a positive result and ignore duplicate bind messages.

This brings up an important, yet subtle point: ⚠️ **_link definition values cannot be changed at runtime_**. If you have a link definition for an HTTP server link for an actor, and that definition contains a `PORT` value of `8080`, then there is nothing that can be done to change that value at runtime. This is a security measure first and foremost, and a reliability/consistency measure secondarily. If you could change the configuration at runtime, then you could very easily have divergence between the actual and declared configuration. Further, the consequences of changing configuration values at runtime are unknowable by the platform... sometimes even the application developers may not know the consequences.

As a result, if you truly must change configuration at runtime, you must _remove_ a link definition (causing the actors and providers involved to "un bind" and release all related resources, HTTP ports, etc) and then add back a new one with the new values.
