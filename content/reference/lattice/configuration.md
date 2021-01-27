---
title: "Configuration"
date: 2018-12-29T11:02:05+06:00
weight: 4
draft: false
---

Configuring lattice is pretty straight-forward. The main goal in lattice configuration for a wasmCloud host is to supply connection parameters to a NATS server. For anonymous authentication this could simply be the URL and port of the NATS server, or for NATS 2.x decentralized authentication, this can include the client secret (seed key) and the user JWT (the combination of which is referred to as a "credentials file").

For more information on the various ways you can connect to and configure NATS servers, please consult the [NATS documentation](https://docs.nats.io/developing-with-nats/connecting).

### Configuring the wasmCloud Binary

There are a number of environment variables that can be supplied for the wasmCloud binary. These can also be supplied via command-line options, though for security purposes it's often preferred to use environment variables as those values cannot be retrieved from the global process list.

| Variable | Description |
| :--- | :--- |
| `RPC_HOST` | NATS host for lattice RPC |
| `RPC_PORT` | NATS port for lattice RPC |
| `RPC_JWT` | User JWT for lattice RPC |
| `RPC_SEED` | User seed for lattice RPC |
| `RPC_CREDS` | Creds file for lattice RPC (replaces jwt+seed) |
| `CONTROL_HOST` | NATS host for lattice control interface |
| `CONTROL_PORT` | NATS port for lattice control |
| `CONTROL_JWT` | User JWT for lattice control |
| `CONTROL_SEED` | User seed for lattice control |
| `CONTROL_CREDS` | NATS creds file for lattice control (replaces jwt+seed) |

### Configuring the wasmCloud Host API

If you are building your own custom wasmCloud host, then you'll be using the [wasmcloud-host](https://crates.io/crates/wasmcloud-host) crate and the `HostBuilder` struct. Please consult the [Rust documentation](https://docs.rs/wasmcloud-host) on this crate for information on how to create hosts. It's worth pointing out that the host and host builder prevent runtime changes to critical components, like the lattice configuration. While you can add and remove actors and capability providers at runtime, you cannot reconfigure the lattice connection while the host is running.
