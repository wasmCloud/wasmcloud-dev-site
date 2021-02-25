---
title: "Creating a waPC Language Binding"
date: 2018-12-29T11:02:05+06:00
weight: 7
draft: false
---

To create a language binding for a waPC **guest module**, there are a couple of steps you need to take. To create a **host runtime**, you'll want to follow the examples in the [wapc-go](https://github.com/wapc/wapc-go) or [wapc-rust](https://github.com/wapc/wapc-rust) repositories. Hosts must implement and expose the [FFI](../ffi) functions, while guests must expose the guest-owned functions and consume those exposed by a host runtime.

1. Create a reusable guest library that all guest modules can use, including wasmCloud actors. Examples of guest libraries can be found in the following repositories:
    * [wapc-guest-rust](https://github.com/wapc/wapc-guest-rust) - Rust
    * [wapc-guest-tinygo](https://github.com/wapc/wapc-guest-tinygo) - TinyGo
    * [wapc-guest-zig](https://github.com/wapc/wapc-guest-zig) - Zig
    * [as-guest](https://github.com/wapc/as-guest) - AssemblyScript

    Guest libraries must expose the `__guest_call` function and consume all of the host's functions in the appropriate sequence. You can also choose to provide a number of conveniences that might be idiomatic for your language of choice.

1. Add a language to the `widl` code generator [here](https://github.com/wapc/widl-codegen/tree/master/src/language).
1. Add the appropriate language-specific templates [here](https://github.com/wapc/basic-modules/tree/master/templates).
    The code generator for `widl` and the corresponding templates are used to create reusable communications libraries that can be shared between a capability provider and an actor. For wasmcloud, we have ours in the [actor-interfaces](https://github.com/wasmcloud/actor-interfaces) repository.
1. The only difference between a **waPC** guest module and a wasmCloud **actor** is that actors are required to interpret and respond to a health request. You can see from the multiple [actor-core](https://github.com/wasmCloud/actor-interfaces/tree/main/actor-core) libraries how you can upgrade a regular waPC module to a wasmCloud actor. You will need to implement a corresponding "actor core" library for your new language that allows actors to respond to the health request message.

You can find more details on `widl`, the `widl` specification, and creating waPC language bindings at [wapc.io](https://wapc.io).
