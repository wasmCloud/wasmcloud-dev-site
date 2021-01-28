---
title: "wasmCloud Shell (wash)"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

**_wash_** (the _wasmCloud Shell_) is a single command-line interface (CLI) to handle all of your wasmCloud tooling needs. This CLI has a number of sub-commands that help you interact with a subset of the wasmCloud ecosystem:

* `claims` - Functions related to reading, writing, and verifying claim attestations
* `keys` - Functions for generating, listing, and working with ed25519 encryption keys used for signatures
* `lattice` - Interact with a live, running lattice
* `par` - Functions for working with [provider archive](../host-runtime/provider-archive)s, bundles of native libaries and claims for a capability provider.
* `reg` - Functions for interacting with OCI registries
* `up` - Starts a wasmCloud host as well as a [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) prompt for interacting with the host and its lattice.

For more detailed information on what **wash** does and how you can use it either from the command line or from Rust, take a look at the Rust documentation for the [wash-cli](https://crates.io/crates/wash-cli) crate.
