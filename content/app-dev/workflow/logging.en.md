---
title: "Logging"
date: 2022-09-10T00:00:00+00:00
weight: 2
draft: false
---


wasmCloud includes support for logging within your application. 

## Logging from an actor

Logging for actors is done through the builtin logging capability provider. The actor must be [signed with the capability](/app-dev/std-caps/) `wasmcloud:builtin:logging`. Log messages emitted by actors are added to the wasmcloud host's log output.
The `wasmbus-interface-logging` crate includes macros `debug`, `info`, `warn`, `error`, which are similar to the standard `log` crate macros.

To enable logging from a Rust actor, import the [logging interface](https://docs.rs/wasmcloud-interface-logging/latest/wasmcloud_interface_logging/) in your Cargo.toml:
```toml
wasmcloud-interface-logging = { version = "0.7.1", features = [ "sync_macro" ]}
```
(Without the `sync_macro` flag, the macros can only be used inside a rust `async` function. The flag enables macros that can be used inside sync functions and closures.)

```rust
use wasmcloud_interface_logging::debug;
debug!("hello {}", "world");
```

Don't forget to add `wasmcloud:builtin:logging` to the actor's [claims](/app-dev/std-caps/) For builtin capability providers, it is not necessary to create a LinkDefinition - the actor will be automatically linked to the logging provider when the actor starts.


## Logging from a capability provider

As with actors, log messages from a capability provider are included in the host's log output.

The simplest and most general way to log messages from a rust capability provider is to use the `tracing` crate. The tracing crate includes macros `debug!`, etc. that are compatible with their counterparts in the `log` crate.
```rust
use tracing::debug;
debug!("all-ok: {}", &message);
```

Inside Cargo.toml, import tracing:

```toml
tracing = "0.1"
```

If you need to use the `log` crate, instead of `tracing`, or if your provider has dependencies that use the `log` crate, add `tracing-log` to Cargo.toml, and initialize it in your provider's main function (before calling `provider_*`). This setting will send output from log macros through the tracing subsystem.

```rust
tracing_log::LogTracer::init()?;
```

### Logging from inside `main`

Logging is initialized by wasmbus-rpc's provider start functions (`provider_main`, `provider_start`, or `provider_run`), so if you want to log messages inside `main` before calling one of the `provider_*` methods, you'll need to use `println!` or `eprintln!` instead of a log macro.


### Setting log level at runtime

To adjust the log verbosity level, set the environment variable `RUST_LOG` before starting the wasmcloud host, e.g., `RUST_LOG=debug`. If debug level verbosity is too noisy, you can set fine-grained filters on a per-module or per-function basis, as described in the [Rust Cookbook](https://rust-lang-nursery.github.io/rust-cookbook/development_tools/debugging/config_log.html).



