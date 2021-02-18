---
title: "Combining Capabilities"
date: 2018-12-29T11:02:05+06:00
weight: 4
draft: false
---

Many of the examples we've covered in the documentation have been actors that make use of a single capability. For example, we often illustrate a number of topics by creating an actor that responds to HTTP server requests with some equivalent of "Hello world!" This is just the tip of the iceberg! The real power of wasmcloud shines when you start plugging in multiple actor interfaces to a single actor.

### Combining Key-Value and HTTP Server

The [kvcounter](https://github.com/wasmcloud/examples/tree/main/kvcounter) example provides an illustration of both handling HTTP requests from a web server and read/write operations against a key-value store.

In the previous section on using standard capabilities, we covered how easy it is to simply add a project reference to an actor capability. You are free to pick and choose multiple actor references to create incredibly powerful actors that accomplish a great deal with just a small amount of code.

### Architectural Guidance

For more thorough examples of actors that combine multiple capabilities (including custom-built, third-party capabilities), check out the [reference applications](/reference/refapps/) section of the reference guide.
