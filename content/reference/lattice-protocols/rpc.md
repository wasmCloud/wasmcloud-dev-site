---
title: "RPC"
date: 2018-12-29T11:02:05+06:00
weight: 2
draft: false
---

At its core, the _lattice_ is essentially a _Remote Procedure Call (RPC)_ bus layer built on top of the NATS message broker. When the lattice
is enabled, participants in lattice RPC must conform to a set of standards in order to be able to send and handle messages.

Lattice RPC supports the following interaction modes:

* Actor-to-Actor
* Actor-to-Provider
* Provider-to-Actor

### The Invocation

All RPC interactions within the lattice involve the sending of an `Invocation` and the receipt of an `InvocationResponse`. Even
if the operation is "fire and forget", the `InvocationResponse` is required to indicate a successful acknowledgement of the invocation.

All `Invocation` and `InvocationResponse` objects are serialized using message pack. For the most current definition of these structs, you'll want to check out the code in the [dispatch](https://github.com/wasmCloud/wasmCloud/blob/main/crates/wasmcloud-host/src/dispatch.rs) module of the [wasmcloud-host](https://crates.io/crates/wasmcloud-host) crate. The following is a description of the fields on an invocation:

* `origin` - The sender of the invocation. This is a `WasmCloudEntity` enum and can be an actor or a provider.
* `target` - The intended recipient of the invocation. Can be an actor or a provider.
* `operation` - The operation string of this invocation, e.g. `HandleRequest` or `DeliverMessage`
* `msg` - The raw bytes of the message. wasmCloud assumes these bytes are messagepack encoded.
* `id` - A GUID assigned to this invocation. The response to this invocation will carry this value for correlation.
* `encoded_claims` - A JSON Web Token (JWT) containing the claims for this invocation.
* `host_id` - The host from which the invocation originated.

### Invocation Claims

Lattice RPC enforces a measure of security in RPC calls over and above the security required to obtain a NATS connection. This
means that if a malicious actor were to compromise the credentials used by a wasmCloud host to connect to the lattice, this
same entity would have to generate its own host ID, a new host key, and figure out how to sign invocations with its own key. This would give monitoring tools the chance to notice an unexpected host appear and react accordingly.

In the future, we may use issuer keys to authenticate the hosts themselves, making it virtually impossible to spoof a host in a lattice using compromised NATS credentials.

The claims data structure can be found in the [wascap](https://github.com/wasmCloud/wascap/blob/main/src/jwt.rs) crate. The claims contain a URL for the origin, a URL for the target, and a hash of the invocation itself. This guarantees that an invocation
cannot be picked up and altered by a man-in-the middle without violating the security check performed by the receiving wasmCloud host.

### Actor Subscriptions

All actors that are in hosts connected to a lattice will **queue** subscribe to incoming invocations on the following topic:

`wasmbus.rpc.{namespace}.{actor public key}`

So, as an example, the `echo` sample in wasmCloud's official OCI registry would subscribe to the following topic if no explicit
namespace prefix were supplied:

`wasmbus.rpc.default.MBCFOPM6JW2APJLXJD3Z5O4CN7CPYJ2B4FTKLJUR5YR5MITIU7HD3WD5`

If you have sufficient logging enabled, you'll see the subscription subjects in the wasmCloud binary's `stdout` logs.

The use of a queue subscription is important because it causes NATS to randomly choose a target from among any of the running
instances of a subscriber.

### Capability Provider Subscriptions

Capability providers also **queue** subscribe to incoming invocations from _linked_ actors on the following topic pattern:

`wasmbus.rpc.{namespace}.{provider public key}.{provider link name}`

For example, if the NATS message broker capability provider were loaded into a host using the `backend` link name on
a lattice with a namespace prefix of `prod`, the subscription topic for the capability provider would be:

`wasmbus.rpc.prod.VADNMSIML2XGO2X4TPIONTIC55R2UUQGPPDZPAVSC2QD7E76CR77SPW7.backend`

The link names allow multiple instances of the same capability provider to be started with different configurations/purposes. For example, one common use of link names we've used is to use the NATS message broker with a `backend` and a `frontend` set of logical link names, which allows the same actor to be bound to the NATS provider twice with 2 logical purposes.
