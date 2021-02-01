---
title: "Creating Capability Providers"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

Creating a capability provider is one of the more involved tasks when working within the wasmCloud ecosystem. First, you need to decide if you're creating a new provider for an existing contract or if you're creating an entirely new contract. If you don't need to create a new contract, then you can skip to the second step in this guide.

### The Sample Use Case

For the purposes of this guide, we're going to be creating a capability provider for a _payments service_ that accepts payments from customers. When composing applications with actors and capabilities, actors in an ecommerce setting may contain business logic that includes the need to accept payments from customers. There are a couple of ways we could solve this problem:

* **Embed Vendor-Specific Knowledge** - In this scenario, an actor might make use of an _HTTP Client_ capability provider, and potentially use providers like a _key-value store_ and even an _event stream_. The actor could be performing a complex transactional dance of creating and retrieving tokens, accessing multiple data sources, and even making potentially long-lived synchronous calls to third parties over HTTP. Despite all our best efforts at isolating business logic in the self-contained unit of an actor, this actor would require recompilation and redeployment if any aspect of _how_ the company accepted payments were to change.
* **Create a Capability Provider** - Hide the implementation details of accepting payments behind the contract of a capability provider, allowing for those details to be swapped at runtime without any impact on running actors.
* **Create another Actor** - We could always defer the problem, and ask another actor to perform the payment processing for us. However, this just "kicks the can down the road" because that new actor would now have the same problem we have with the original one--_how to perform the actual payment processing?_

Obviously, as this is a guide for creating new capability providers, we've decided that for this use case, **creating a new capability provider** makes the most sense.

Creating a new capability provider involves:

1. [Contract-Driven Design](./cdd)
1. [Creating a Rust Capability Provider](./rust)
1. [Create a Provider Archive](./create-par)
1. [Using the New Capability Provider in an Actor](./consuming)
