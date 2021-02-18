---
title: "Lattice Cache Providers"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

The lattice, as a single cohesive entity, needs to keep track of a certain amount of information and metadata. This includes things like the list of actor claims that have been discovered in the lattice, OCI reference mappings between registry URLs and actor and provider public keys, link definitions, etc. The lattice _does not_ generate or maintain state in the way you might imagine an event-sourced system does. It does not have event streams that generate aggregates. For the most part, important events take place in a host/lattice that then produce _idempotent **puts**_ into a key-value store.

### NATS Replication Provider (Default)

The default lattice cache provider is the [NATS cache provider](https://github.com/wasmcloud/capability-providers/tree/main/nats-kvcache). This is an in-memory key-value store (basically a `HashMap`). When you run as a single, standalone host this capability provider is run without a connection to NATS, and acts in a way that is virtually indistinguishable from a simple `HashMap`.

If a lattice connection has been provided to the host, then the **NATS Cache Provider** will enable cache replication via NATS. This is done by publishing state changes (idempotent **puts**) on a specific NATS subject. All wasmcloud hosts listening on that same lattice will receive those messages and use them to update their cache.

When this capability provider starts, it will make a special type of request called a `Replay` request. It does this by publishing a message on the replay subject. In response, the wasmcloud host that NATS selected to receive that message will begin streaming a list of cache operation commands. This then allows this capability provider to reconstitute cache state at startup time. It is this kind of replication that ultimately allows things like link definitions to be placed in the lattice cache in arbitrary order relative to the starting of those participants (because each host will eventually become aware of all link definitions).

The NATS key-value replication provider is the default provider used by wasmcloud hosts, and is suitable for development, experimentation, troubleshooting, and even operating in small lab environments. However, if message loss (and the subsequent cache inconsistency that arises from it) is not tolerable (e.g. _production_), then you should select an alternative key-value store provider to serve as the basis of your lattice cache.

⚠️ **WARNING** - _Do not use the default cache provider in environments where repeated message loss can result in cache drift and inconsistency. This provider should not be used in production_.

### Alternative Cache Providers

Choosing an alternative cache provider is as simple as providing a valid URL for an OCI image reference. The capability provider specified need only satisfy the `wasmcloud:keyvalue` capability contract. This means that you can swap out the in-memory, NATS-replicated default provider for something more robust, like **Redis**, **Cassandra**, or **Consul**, or any number of other durable, resilient, distributed key-value stores designed for supporting production-grade caches.

Simply call the `with_lattice_cache_provider()` function on the `HostBuilder` struct in the [wasmcloud-host](https://crates.io/crates/wasmcloud-host) crate to set the OCI URL of the provider you wish to use for the lattice cache.

