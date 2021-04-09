---
title: "Actors"
date: 2018-12-29T11:02:05+06:00
weight: 4
draft: false
---

An **_actor_** is the smallest unit of deployable, portable compute within the wasmcloud ecosystem. Actors are small WebAssembly modules that can handle messages delivered to them by the host runtime and can invoke functions on [capability providers](../capabilities), provided they have been [granted the appropriate privileges](../security).

### Single-Threaded

_Concurrency is hard_. Even when we get concurrency correct, it's still hard. Building systems that work properly either through multi-threading or through so-called "green threads" or "coroutines" is difficult. Concurrency introduces friction in writing new code, maintaining old code, troubleshooting, and routinely wreaks havoc on production systems.

Developers want to write business logic, without having to worry about the concurrency properties of the surrounding environment. In alignment with [the actor model](https://en.wikipedia.org/wiki/Actor_model), wasmcloud actors are single-threaded _internally_. The surrounding environment provided by the host runtime may have varying levels of concurrency support, or could even have entirely different concurrency models depending on whether it's running in a browser, on a constrained device, or in the cloud. The code we write for actors should not have to worry about these things.

Though we shouldn't have to worry about the surrounding concurrency model, we need to be aware that our single-threaded code can back things up and produce congestion. Therefore, when developing _message handlers_ for actors, we need to embrace the design of performing small amounts of work in a "get in and get out fast" approach: divide the work to be done into the smallest bits possible, and perform each bit as fast as we can. This approach maximizes the benefits of _external concurrency_ while still keeping the code we write blissfully synchronous.

### Reactive

Actors are [reactive](https://en.wikipedia.org/wiki/Reactive_programming). An actor cannot initiate any action on its own, it can simply _react_ to outside stimuli in the form of messages delivered by the host. Actor developers register message handlers that receive input and return messages as output, as shown in the following example that receives a bank account query and responds with the bank account value:

```rust
#[wasmcloud-actor-core::init]
pub fn init() {
    bank::Handlers::register_handle_query(handle_balance_inquiry);
    // other handlers...
}

fn handle_balance_inquiry(query: bank::BalanceInquiry) -> HandlerResult<bank::Balance> {
    let balance = // ... query
    Ok(bank::Balance{ 
        account: query.account,
        balance
    })
}
```

While the preceding actor code could communicate with capability providers (e.g. a _key-value store_ to retrieve the account balance), it could only do so in response to a message being delivered from the host.

### Communication by Abstraction

wasmcloud actors are _loosely coupled_ with the capability providers they use for non-functional requirements. An actor doesn't communicate with **Redis** or **Cassandra** or **Consul**, instead it communicates with a generalized abstraction over _key-value stores_. Under the hood, each of these abstractions is represented by a contract or **interface**. As long as the capability provider implements the correct interface contract, it should be considered compatible with your actor. An actor written against the key-value store abstraction should be able to work with _any_ key-value store, and even have that store swapped live at runtime without requiring a rebuild or redeploy.

There are a number of _first-party_ [actor interfaces](https://github.com/wasmcloud/actor-interfaces) which we maintain in our Github organization. Additionally, the community can build their own public capability providers and enterprises are free to build their own internal, proprietary capability providers that expose private enterprise or corporate functionality.

### Secure

Actors are secure by default. Because actors are WebAssembly modules that cannot use the WASI extensions, these modules are physically incapable of interacting with any operating system functionality on their own. The only way actors can affect their external environment is through the use of a capability provider.

Actors must be explicitly granted access to each capability provider contract (abstraction/interface), otherwise the host runtime will not allow it to make calls to that provider or receive messages from it. Granting access to capabilities is discussed more in the [security](../security) section of the reference, but the short version is that each actor's WebAssembly module contains an embedded JSON Web Token (JWT) that holds claims, including claims attesting to which providers the actor is allowed to use.
