---
title: "Creating Capability Providers"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

Creating a capability provider is one of the more involved tasks when working within the wasmCloud ecosystem. First, you need to decide if you're creating a new provider for an existing contract or if you're creating an entirely new contract. If you don't need to create a new contract, then you can skip to the second step in this guide.

### The Sample Use Case

For the purposes of this guide, we're going to be creating a capability provider for a _payment service_ that accepts payments from customers. When composing applications with actors and capabilities, actors in an ecommerce setting may contain business logic that includes the need to accept payments from customers.

To solve this problem, we're going to hide the implementation details of accepting payments behind the contract of a capability provider, allowing for those details to be swapped or re-configured at runtime without any impact on running actors.

Creating a new capability provider involves:

1. [Contract-Driven Design](./cdd)
1. [Creating a Rust Capability Provider](./rust)
1. [Create a Provider Archive](./create-par)
1. [Using the New Capability Provider in an Actor](./consuming)
