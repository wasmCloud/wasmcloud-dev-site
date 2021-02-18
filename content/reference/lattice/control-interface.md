---
title: "Control Interface"
date: 2018-12-29T11:02:05+06:00
weight: 8
draft: false
---

The lattice control interface provides a way for clients to interact with the lattice to issue control commands and queries. This interface is a message broker protocol that supports functionality like starting and stopping actors and providers, declaring link definitions, performing function calls on actors, monitoring lattice events, holding _auctions_ to determine scheduling compatibility, and much more.

The core message broker protocol can be used by any client capable of connecting to NATS. There is also a [Rust crate](https://github.com/wasmcloud/wasmcloud/tree/main/crates/control-interface) that provides a convenient API for accessing the control interface. Please see the Rust documentation for the crate on how to use the Rust API.

The following is a list of the operations supported by the control interface.

### NATS Control Interface

All of the control interface messages published on NATS topics use a standard prefix. This prefix is `wasmbus.ctl.{namespace}` where `namespace` is a string used to differentiate one lattice from another. Note that this namespace must correspond to the namespace prefix of the lattice you intend to control. As an example, if your lattice namespace prefix is `prod`, then you would subscribe to the lattice event stream at `wasmbus.ctl.prod.events`.

⚠️ You need to ensure that your namespace prefix does not contain any characters that cannot appear in a NATS subject.

All control interface messages are serialized as _message pack_ payloads that conform to the schema found in the [actor interfaces](https://github.com/wasmcloud/actor-interfaces) repository.

| Topic | Type | Description |
| :--- | :--- | :--- |
| `{pre}.events` | Sub | A stream of lattice events |
| `{pre}.auction.provider` | Req | Hold an auction for starting a provider |
| `{pre}.auction.actor` | Req | Hold an auction for starting an actor |
| `{pre}.cmd.{host}.la` | Req | Tell a host to start (_launch_) an actor |
| `{pre}.cmd.{host}.sa` | Req | Tell a host to stop an actor |
| `{pre}.cmd.{host}.lp` | Req | Tell host to launch provider |
| `{pre}.cmd.{host}.sp` | Req | Tell host to stop provider |
| `{pre}.cmd.{host}.upd` | Req | Tell host to live-update actor |
| `{pre}.get.links` | Req | Query link definition list |
| `{pre}.get.claims` | Req | Query claims cache |
| `{pre}.get.{host}.inv` | Req | Query host running inventory |
| `{pre}.get.hosts` | Coll | Collect list of all running hosts |

Operations of type `Sub` are subscribe-only. A lattice control interface client may subscribe to this event stream, but _should never publish to it_. A good security scenario is to have a different set of lattice connection credentials for the wasmcloud host than you use for the control client. This allows you to prevent the control client from publishing to potentially dangerous topics.

Operations of type `Req` are requests that receive replies. These replies may be simple acknowledgements and may not directly indicate completion of work.

Operations of type `Coll` are _collect_ or _scatter-gather_ operations that issue a request on a subject and then wait for some given period of time to collect responses. Use caution with collect/gather operations because if you do not wait long enough, you may receive incomplete responses.
