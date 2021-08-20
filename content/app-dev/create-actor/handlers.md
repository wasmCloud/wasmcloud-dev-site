---
title: "Define Message Handlers"
date: 2018-12-29T11:02:05+06:00
weight: 4
draft: false
---

Our new actor has come pre-equipped with a message handler that returns an empty string in the body of the HTTP response to all callers. In this exercise, we're going to modify the business logic slightly so that instead of the empty string, we return "Hello, " concatenated with the value of the `name` property as passed on a query string. While this is a classic sample, the ease with which you can change things should feel different (and hopefully easier) than traditional development.

Now let's modify the `handle_request` function to be a proper "Hello world" sample:

```rust
TODO
```

To make sure this actor is compiled and ready to run, we can simply run `make` in the project directory and `cargo` will compile it and generate a `.wasm` file and then the `wash` CLI will sign the module with a set of claims that includes access to the HTTP Server capability.
