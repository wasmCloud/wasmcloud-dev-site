---
title: "Getting Started"
date: 2018-12-29T11:02:05+06:00
weight: 1
draft: false
---

Getting started with application development on wasmCloud is pretty simple. The first thing you'll need to do is follow the [installation](/overview/installation) steps outlined in this guide.

Next, make sure you have the language of your choice installed and ready to go. Today, we support **Rust** but support for other languages will be coming soon. 

Before you can build actors with Rust, you'll need to have the `wasm32-unknown-unknown` target added to 
your environment. You can set this up by executing the following command:

```shell
rustup target add wasm32-unknown-unknown
```

While not yet utilized for wasmCloud development, if you're curious about building WASI modules, you can also add the `wasm32-wasi` target.

Let's get started by creating an [actor](/reference/host-runtime/actors).