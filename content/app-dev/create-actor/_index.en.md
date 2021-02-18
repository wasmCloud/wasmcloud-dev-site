---
title: "Creating an Actor"
date: 2018-12-29T11:02:05+06:00
weight: 2
draft: false
---

Creating an actor with **wasmcloud** involves a few steps. First, you'll need to choose the programming language for your actor. As of the current release, we support the following programming languages for actors:

* Rust
* TinyGo
* AssemblyScript

Next, you'll need to use the appropriate tooling to create a new actor in your language of choice. The current (`0.2.0`) version of the **wash** CLI does not **yet** support code generation or the production of scaffolding. As such, if you want scaffolding for Rust, you should use `cargo generate`, which is what this guide assumes. Check the [actor interfaces](https://github.com/wasmcloud/actor-interfaces) GitHub repository for information on scaffolding for other languages.

This guide will walk you through the following steps to creating an actor:

* [Generate the scaffold](./scaffold)
* [Define message handlers](./handlers)
* [Run the actor](./run)
