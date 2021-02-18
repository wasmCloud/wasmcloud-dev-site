---
title: "Running on Kubernetes"
date: 2018-12-29T11:02:05+06:00
weight: 3
draft: false
---

There are a couple of options for running `wasmcloud` in Kubernetes, and choosing between the two involves weighing your needs and constraints within your target cluster. You can either schedule and configure a wasmcloud host runtime the same way you would any other docker-based workload, or you can populate some or all of your cluster's nodes with [krustlet](https://krustlet.dev/) hosts, which are capable of scheduling wasmcloud actors directly.

### Scheduling in a Pod

Scheduling a wasmcloud container in a pod involves the same activities as scheduling any other kind of container. For more information, consult the [Kubernetes reference documentation](https://kubernetes.io/docs/concepts/workloads/pods/).

### Scheduling Actors Directly via Krustlet

Scheduling actors directly via [krustlet](https://krustlet.dev) means creating Kubernetes resource definitions where the "image" is actually a signed wasmcloud WebAssembly `.wasm` file. In order to be able to do this, you'll need to install and configure krustlet within your Kubernetes environment. Then, you will need to use the [krustlet wasmcloud provider](https://github.com/wasmcloud/krustlet-wasmcloud-provider) to schedule actors.
