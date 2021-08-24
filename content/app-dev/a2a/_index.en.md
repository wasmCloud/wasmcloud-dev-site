---
title: "Actor to Actor Calls"
date: 2018-12-29T11:02:05+06:00
weight: 6
draft: false
---

The ability for one actor to call another actor is critical to being able to create _composable_ actor systems. wasmcloud supports RPC-style communication between actors, even if those actors are running in hosts scattered across disparate infrastructure, connected only via the [lattice](/reference/lattice).

### Identifying Actors

The single most important task when calling other actors is to identify the target actor. wasmCloud supports two ways of identifying another actor for invocation purposes:

* Public Key (Subject)
* Call Alias

#### Public Key (Subject)

Every single actor _must_ have a subject, which is a 56-character public key made of up uppercase letters. These subject strings always start with the capital letter **M** (module). When actors are signed with embedded capability claims, they are always required to have a _subject_ claim. This means that invocation by public key is the **most reliable** means for locating target actors, though it can often impose some amount of friction on the part of the calling actors.

#### ⚠️ Caution
You must be cautious when hard-coding public keys in your actors. If your software pipeline involves signing actors with _different private keys_ across different environments, then that will change the public keys of those actors and result in target actors not being found during invocations at runtime.

#### Call Alias

One potentially appealing alternative to the guaranteed uniqueness and reliability of public keys is the use of a _call alias_. When actors are signed, they can optionally be signed with a `call_alias` claim. This alias must be an alphanumeric string that can optionally have separator/hierarchy characters like `.` or `/` or `_`, e.g. `accounting` or `accounting/invest`.

When an actor with a call alias is started, the entire lattice in which that actor resides will be informed of the claim on this alias. If the alias is _already claimed_, then the newly started actor will _not_ be able to use it for identification purposes. This is the downside to using aliases: it is up to you to ensure that they are unique _per lattice_. They do not need to be globally unique.

Call aliases, when used appropriately, can provide consistent, developer-friendly naming conventions for locating target actors and often serve a similar purpose as the concept of "discovery servers" or "discovery services" in more traditional microservice environments.

### Using the Actor Core API

All actors in the wasmCloud ecosystem are capable of making calls to other actors. The wasmCloud host runtime will, by default, allow any two actors to communicate with each other. If you wish to apply a security policy to actor-to-actor invocations (optionally rejecting some invocations), then you'll want to look into our [OPA]() integration.

The following code snippet illustrates the classic "ping-pong" actor-model example of one actor invoking another:

```rust
TODO
```

### Communication Contract

When you design your distributed application to allow actors to communicate with each other, you need some way to declare the _shape_ or _schema_ of the data they will use to communicate. This can easily be done using **smithy** models that declare only "actor receive" services. One actor can then invoke the "actor receive" function on another actor, enabling RPC between actors.

For more information on interface creation, take a look at the [actor interfaces](/app-dev/create-provider/new-interface) section of the documentation.

