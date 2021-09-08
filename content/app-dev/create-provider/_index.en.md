---
title: "Creating Capability Providers"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

When you decide to create a new capability provider, you have two options:
* Create a provider for a brand new interface contract.
* Create a provider for an existing interface contract.

In this guide, we're going to cover all the steps for creating a capability provider, including creating a new interface. If you're not interested in defining interfaces and working with code generation, you can skip to the second step in this guide.

### The sample use case

For the purposes of this guide, we'll create a capability provider for a _payment service_ that accepts payments from customers. When composing applications with actors and capabilities, actors in an ecommerce setting may contain business logic that includes the need to accept payments from customers.

To solve this problem, we're going to hide the implementation details of accepting payments behind the contract of a capability provider, allowing for those details to be swapped or re-configured at runtime without any impact on running actors.

Creating a new capability provider involves:

1. [Creating a new interface](./new-interface)
1. [Creating a capability provider](./rust)
1. [Creating a provider archive](./create-par)
1. [Using the new capability provider in an actor](./consuming)
