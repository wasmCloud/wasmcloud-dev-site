---
title: "Working with Leaf Nodes"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

A lattice **_leaf node_** is nothing more than a wasmCloud host connected to a lattice via a [NATS leaf node](https://docs.nats.io/nats-server/configuration/leafnodes). Please consult the NATS documentation for detailed information on how to create, configure, manage, and deploy NATS leaf nodes.

What leaf nodes enable for lattice is a way to segment or isolate traffic and/or security boundaries. For example, you can start a NATS leaf node running on the same host as a wasmCloud process. This leaf node could have its own unique (and private) set of credentials used for connecting to the NATS server on the other side of the node. You could then configure the wasmCloud lattice configuration to anonymously connect to the leaf node running on `localhost`. For more information on the pros and cons of this pattern, consult the [security patterns](../security-patterns) lattice reference.

This segmentation or isolation is _ideal_ for bridging disparate infrastructure. Using leaf nodes at the "edges" or boundaries of your infrastructure allows the lattice to continue to expose a single, flat topology while NATS and leaf nodes take care of all the hard work of optimizing traffic patterns for the interest graph across an arbitrarily complex network.

For example, you could use a leaf node to service all of the lattice traffic for an on-premise sub-portion of the network, which could be a warehouse or a retail facility or even a cluster of IoT devices. This leaf node could then connect back to a cloud, the same cloud connected to by multiple other leaf nodes that allow traffic bridging from wasmCloud hosts running in browsers, on IoT devices, on laptops, and in multiple clouds.

We are not exaggerating at all when we say that the true power of the wasmCloud lattice comes from NATS and NATS leaf nodes.
