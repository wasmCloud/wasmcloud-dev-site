---
title: "Developer Workflows"
date: 2018-12-29T11:02:05+06:00
weight: 8
draft: false
---

As a wasmCloud developer, there are a number of common day-to-day workflows that you will experience. Which workflows you run through and how often you do so depends entirely on what you're building.

The following is a list of developer workflows _sorted_ from **_most to least common_**.

### Building Actors

The most common thing application developers will do is build actors. Actors encompass pure business logic, and only communicate with non-functional requirements through capability providers and abstract [interfaces](/interfaces).

Once you've established a dependency on a library that exposes the interface abstraction you're looking for, you can start your iteration loop.

The developer's _iteration loop_ for building an actor looks something like this:

1. Make code changes
1. Compile and sign the WebAssembly module, creating a `_s.wasm` file (using `make` in an actor project)
1. Test the module
    1. In the wasmCloud web dashboard
        1. Use the _From File (Hot reload)_ form to automatically watch actor files for changes
        1. Script `wash call` commands to invoke the actor
        1. Modify your actor code, run `make` to recompile and re-sign the actor, repeat the above step
    1. Leverage the `wasmcloud:testing` interface and the [test provider](https://github.com/wasmCloud/wasmcloud-test)
1. _Repeat_

### Building Providers

The workflow for building a capability provider is similar to that of building an actor. Once you've located and declared a dependency on the _interface_ implemented by your capability provider, the _iteration loop_ looks something like this:

1. Make code changes
1. Execute tests (`make test`)
1. Compile native executable binary
1. Create and sign JWT
1. Embed JWT and executable in a `.par.gz` (provider archive) file. (Steps 3-5 can be done with the single command `make` using the generated project Makefiles).
1. Publish `.PAR` file to local or remote OCI registry (`make push`)
1. Test/Utilize the provider in the context of a host/lattice
1. _Repeat_

### Creating new interfaces

Creating a new wasmCloud _interface_ is probably the least commonly performed task, as generating new abstractions happens far less often than either consuming or providing that abstraction.
Once you've created the scaffolding for a new interface library (which is available as a `wash new` command), the _iteration loop_ looks something like this:

1. Make changes to the **Smithy** model (`.smithy` file)
2. [Check it](/interfaces/tips/lint-validate/) with `wash lint` and `wash validate`
3. Build it (`make` or `cargo build`, if it has Rust code)
4. Test the library

When the library is ready to release, it can be published. For example, interfaces made with our Rust SDK can be published to [crates.io](https://crates.io).

## Running a local OCI registry

While it isn't called out as a specific pre-requisite, _many_ of the steps in the developer iteration loops involve interacting with an OCI registry. Unless you've got a public one that you can use, you'll likely want to use a local one for testing.

To start a local OCI registry, download the [sample Docker Compose file](https://raw.githubusercontent.com/wasmCloud/examples/main/docker/docker-compose.yml) into the current folder and run

```bash
docker compose up -d registry
```

Once it's running, you can push actors and capability providers to the registry via the `wash reg` set of commands (or `make push`, if using the wasmCloud Makefiles).

### Allowing unauthenticated OCI registry access

The wasmCloud host runtime will, by default, require that all OCI references use authentication in order to resolve and download. This is a security measure that is enabled by default to keep the system as secure as possible.

However, if you're running the local docker-supplied registry with its default settings, that registry will not have any authentication requirements. If you want your wasmCloud host to be able to talk to this registry, you'll need to enable unauthenticated OCI registry access.

This can be done by setting the environment variable `WASMCLOUD_OCI_ALLOWED_INSECURE` to include the URL of your local registry, e.g. `localhost:5000`. You can either supply this as an environment variable directly when you start a local wasmCloud host via `iex` or the release binary, or you can modify your shell profile to always set this variable on your development workstation.

### Purging the OCI cache

The wasmCloud host runtime caches the files that it receives from OCI registries beneath whatever `temp` directory your operating system prefers. Because images in an OCI registry are supposed to be _immutable_ (another reason we recommend against using `latest` when requesting an image version), the wasmCloud host has no reason to automatically purge or overwrite these files in the cache.

During your local development iterations, you will likely find yourself pushing the same file with the same OCI reference over and over again. In order for the wasmCloud host to see these changes, you'll need to _drain_ the wasmCloud host cache. This can be done by executing one of the variants of `wash drain`, such as `wash drain all`.
