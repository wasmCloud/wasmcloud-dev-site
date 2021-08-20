---
title: "Creating Capability Providers"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

When you decide to create a new capability provider, you have two options:
* Create a provider for a brand new interface contract
* Create a provider for a previously existing interface contract.

In this guide, we're going to cover all of the tasks of creating a capability provider, including creating a new interface. If you're not interested in defining interfaces and working with code generation, then you can skip to the second step in this guide.

### The Sample Use Case

For the purposes of this guide, we're going to be creating a capability provider for a _payment service_ that accepts payments from customers. When composing applications with actors and capabilities, actors in an ecommerce setting may contain business logic that includes the need to accept payments from customers.

To solve this problem, we're going to hide the implementation details of accepting payments behind the contract of a capability provider, allowing for those details to be swapped or re-configured at runtime without any impact on running actors.

Creating a new capability provider involves:

1. [Creating a New Interface](./new-interface)
1. [Creating a Capability Provider](./rust)
1. [Create a Provider Archive](./create-par)
1. [Using the New Capability Provider in an Actor](./consuming)
