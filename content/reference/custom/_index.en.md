---
title: "Extension and Customization"
date: 2018-12-29T11:02:05+06:00
weight: 3
draft: false
---

wasmCloud--the ecosystem and associated Rust crates--supports a number of different ways in which you can extend and customize the experience.

It has support for pluggable **middleware**, which lets you inject your own code into the pipeline of every execution done by a wasmCloud host.

You can chose to select your own alternative **cache provider** for managing the shared state of a lattice cluster.

You can even create your own pluggable **authorization** code that allows you to further restrict authorization decisions made by the runtime.
