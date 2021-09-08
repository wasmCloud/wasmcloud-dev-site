---
title: "Creating a New Provider"
date: 2018-12-29T11:02:05+06:00
weight: 7
draft: false
---

Creating a capability provider involves creating a native executable. All capability provider executables have the same basic requirements:
* Accept a [Host Data](https://wasmcloud.github.io/interfaces/html/org_wasmcloud_core.html#host_data) structure from `stdin` immediately upon starting the executable. The host data is a base64 encoded JSON object with a trailing newline making it easy to pull from the `stdin` pipe.
* Accept linkdef messages according to the [RPC protocol](../../../reference/lattice-protocols/rpc)
* Communicate with actors via rpc messages defined by a capability contract,
* Respond to periodic health checks

Thankfully, our scaffolding and reusable Rust crates take care of the basic plumbing for satisfying these requirements, letting you focus on messages exchanged with the actor, as defined by your capability contract.

### Create the New Project

Let's create a new provider project

```shell
wash new provider fakepay-provider
```

This command will create a new capability provider called `fakepay-provider`. Lets change to the newly created directory and start writing some code.

### Building the Provider

TODO

Next, we'll create a new private key for this provider, and create and sign a _provider archive_ file.
