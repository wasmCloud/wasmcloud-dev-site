---
title: "Developer Workflows"
date: 2018-12-29T11:02:05+06:00
weight: 8
draft: false
---

As a wasmCloud developer, there are a number of common day-to-day workflows that you will experience. Which workflows you run through and how often you do so depends entirely on what you're building. 

The following is a list of developer workflows _sorted_ from **_most to least common_**.

### Building Actors
The most common thing application developers will do is build actors. Actors encompass pure business logic, and only communicate with non-functional requirements through capability providers and abstract [interfaces](https://github.com/wasmCloud/interfaces).

Once you've established a dependency on a library that exposes the interface abstraction you're looking for, you can start your iteration loop.

The developer's _iteration loop_ for building an actor looks something like this:
1. Make code changes
1. Compile & sign new `.wasm` module (we make this available via `Makefile`)
1. Deploy module to public or local OCI registry
1. Test module in a host
    1. Manually via uploading in wasmCloud web dashboard
    1. Script `wash` commands to `push` module to registry
    1. Leverage the `wasmcloud:testing` interface and the [test provider](https://github.com/wasmCloud/wasmcloud-test)
1. _Repeat_

### Building Providers
The workflow for building a capability provider is similar to that of building an actor. Once you've located and declared a dependency on the _interface_ implemented by your capability provider, the _iteration loop_ looks something like this:
1. Make code changes
1. Execute tests
1. Compile native executable binary
1. Create and sign JWT
1. Embed JWT and executable in a `.PAR` (provider archive) file. This is an automatable task that is included in our scaffolded `Makefile`s.
1. Publish `.PAR` file to local or remote OCI registry
1. Test/Utilize the provider in the context of a host/lattice
1. _Repeat_

### Creating New Interfaces
Creating a new wasmCloud _interface_ is probably the least commonly performed task, as generating new abstractions happens far less than either consuming or providing that abstraction.
Once you've created the scaffolding for a new interface library (which is available as a `wash new` command), the _iteration loop_ looks something like this:
1. Make changes to the **Smithy** model (`.smithy` file)
1. Regenerate code (typically automated as part of the project's build cycle)
1. Compile and test library

And when the library is ready for release, it can be published. For example, interfaces made with our Rust SDK can be published to [crates.io](https://crates.io).

## Running a Local OCI Registry
While it isn't called out as a specific pre-requisite, _many_ of the steps in the developer iteration loops involve interacting with an OCI registry. Unless you've got a public one that you can use, you'll likely want to use a local one for testing. 

To start a local OCI registry, you can use the following `docker` command:

```shell
docker run -d -p 5000:5000 --name registry registry:2
```

Once it's running, you can push actors and capability providers to the registry via the `wash reg` set of commands, which you can of course integrate into your `Makefile` or whatever build script you're using.

### Allowing Unauthenticated OCI Registry Access
The wasmCloud host runtime will, by default, require that all OCI references use authentication in order to resolve and download. This is a security measure that is enabled by default to keep the system as secure as possible.

However, if you're running the local docker-supplied registry with its default settings, then that registry will not have any authentication requirements. If you want your wasmCloud host to be able to talk to this registry, you'll need to enable unauthenticated OCI registry access.

This can be done by setting the environment variable `WASMCLOUD_OCI_ALLOWED_INSECURE` to include the URL of your local registry, e.g. `localhost:5000`. You can either supply this as an environment variable directly when you start a local wasmCloud host via `iex` or the release binary, or you can modify your shell profile to always set this variable on your development workstation.

### Purging the OCI Cache
The wasmCloud host runtime caches the files that it receives from OCI registries beneath whatever `temp` directory your operating system prefers. Because images in an OCI registry are supposed to be _immutable_ (another reason we recommend against using `latest` images), the wasmCloud host has no reason to automatically purge or overwrite these files in the cache.

During your local development iterations, you will likely find yourself pushing the same file with the same OCI reference over and over again. In order for the wasmCloud host to see these changes, you'll need to _drain_ the wasmCloud host cache. This can be done by executing one of the variants of `wash drain`, such as `wash drain all`.