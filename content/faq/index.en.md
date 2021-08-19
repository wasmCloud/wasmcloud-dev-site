---
title: "Frequently Asked Questions"
description: "FAQ"
weight: 6
draft: false
---
 
{{< faq "What is wasmCloud?" >}}
wasmCloud helps developers build, test, scale, deploy and operate services and functions at scale *quickly*.
 
wasmCloud is an application runtime that has been designed to speed up the developer workflow.  An actor model seamlessly separates business logic from specific underlying capabilities.  Common capabilities are included in the runtime and developers may easily create their own.  WasmCloud allows developers to write their business logic in the language of their choice and deploy it everywhere.
{{</ faq >}}
 
 
{{< faq "Why WebAssembly?" >}}
WebAssembly (Wasm) is a highly portable binary instruction format for a stack-based virtual machine.  Wasm is designed as a portable and performant compilation target for programming languages enabling secure, deny-by-default deployment on the web, in the browser, on your server, the edge, or wherever you would like to run your workload.
 
wasmCloud leverages Wasm as a secure and portable foundation - write your functions quickly and run them everywhere.  Wasm is not only fast - it's efficient.
{{</ faq >}}
 
{{< faq "What is an Actor?" >}}
An actor is the smallest unit of deployable, portable compute within the wasmCloud ecosystem. Actors are small WebAssembly modules that can handle messages delivered to them by the host runtime and can invoke functions on [capability providers](reference/host-runtime/capabilities), provided they have been [granted the appropriate privileges](reference/host-runtime/security/).
 
For more information please see [Actors Reference Guide](reference/host-runtime/actors/).
{{</ faq >}}
 
{{< faq "What is a Capability Provider?" >}}
A capability is an abstraction or representation of a non-functional requirement; some functionality required by your [actor](reference/host-runtime/actors/) that is not considered part of the core business logic. For example, as you write an actor that exposes some data over a RESTful endpoint, the HTTP server is not part of your business logic, it is a capability used by your actor.
 
In wasmCloud, capability providers are executables that implement a capability contract. A capability contract has a unique name (e.g. `wasmcloud:httpserver`) and contains a list of operations and their associated data types. By convention, these capability contract IDs are prefixed by a vendor ID (the vendor of the contract, not the specific implementation).
 
For more information please see [Capabilities Reference Guide](reference/host-runtime/capabilities/).
{{</ faq >}}