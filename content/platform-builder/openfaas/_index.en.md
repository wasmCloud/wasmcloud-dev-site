---
title: "Integrating with OpenFaas"
date: 2018-12-29T11:02:05+06:00
weight: 4
draft: false
---

[OpenFaas](https://www.openfaas.com/) makes it easy to deploy functions to Kubernetes. One question we get asked from time to time is how wasmCloud either competes against or integrates with **OpenFaas**. wasmCloud is certainly not a competitor, though there are similarities. It's very easy to design your actors so that they have a single handler function beyond the health check; making them look a lot like lambdas/cloud functions.

### Coming Soon

We have an [issue in our backlog](https://github.com/wasmCloud/capability-providers/issues/27) for developing a wasmCloud _capability provider_ that will act as a "Faas Provider", basically acting as a reverse proxy allowing function invocations to originate from an OpenFaas client and travel through into a lattice-RPC invocation. This will let application and platform developers expose a subset of their actors to OpenFaas invocations.
