---
title: "Release History / Changelog"
description: "This is a high-level overview of the additions, removals, and changes for releases of wasmcloud and related tooling. This is not a complete list. For all of the specific details, check out the release notes for each release in the appropriate repository."
layout: "changelog"
draft: false
---

### Release 0.17.0
{{< changelog "added" >}}
* Added additional logging and error messages to common operations ([PR #143](https://github.com/wasmCloud/wasmCloud/pull/143) and [PR #148](https://github.com/wasmCloud/wasmCloud/pull/148))
* Added the ability to remove a link on the Host API and control interface ([PR #147](https://github.com/wasmCloud/wasmCloud/pull/147))
* Added extensive Rust documentation on the `wasmcloud_host` crate ([PR #151](https://github.com/wasmCloud/wasmCloud/pull/151))
* Added additional queries to the Host API to query the lattice cache. This allows users directly invoking the Host API to retrieve the same information available via the control interface ([PR #153](https://github.com/wasmCloud/wasmCloud/pull/153))
{{</ changelog >}}

{{< changelog "Fixed" >}}
* Fixed an issue where operations could be called on a host that had not started ([PR #142](https://github.com/wasmCloud/wasmCloud/pull/142))
{{</ changelog >}}

### Release 0.16.0
The **0.16.0** release includes multiple bug fixes, documentation improvements, and additional features.

{{< changelog "added" >}}
* Added `bencher` sample for determining wasmcloud performance ([PR #100](https://github.com/wasmCloud/wasmCloud/pull/100))
{{</ changelog >}}

{{< changelog "changed" >}}
* Renamed `wascc-codec` to `wasmcloud-provider-core` ([PR #102](https://github.com/wasmCloud/wasmCloud/pull/102))
* Updated embedded Extras provider to use `wasmcloud:extras` capability contract ([PR #109](https://github.com/wasmCloud/wasmCloud/pull/109))
* Updated `actix-rt` to `2.1.0` which is based on `tokio 1.X`. This is a breaking change for users that embed a `wasmcloud-host` in their Rust program. ([PR #114](https://github.com/wasmCloud/wasmCloud/pull/114) and [PR #135](https://github.com/wasmCloud/wasmCloud/pull/135))
* Changed fields on the `Host` struct to allow it to be sent between threads ([PR #132](https://github.com/wasmCloud/wasmCloud/pull/132))
{{</ changelog >}}

{{< changelog "Fixed" >}}
* Fixed a bug where loading a capability provider caused a SIGSEGV on Linux ([PR #99](https://github.com/wasmCloud/wasmCloud/pull/99))
* Fixed a bug where the live update operation didn't allow for differing OCI references ([PR #128](https://github.com/wasmCloud/wasmCloud/pull/128))
{{</ changelog >}}

### Release 0.15.0

The **0.15.0** release is the single biggest release we've had since the project began. It is essentially a bottom-up rewrite of the entire wasmcloud host runtime. In addition to this internal rewrite, we've incorporated lessons learned from the past year of development and applied those to the way we build capability providers, define actor interfaces, and support the developer experience. The core theme of this release is the ruthless focus on developer experience.

{{< changelog "added" >}}

* Consolidated multiple CLI tools (`nk`, `wascap`, etc) into the new wasmcloud shell [wash](https://github.com/wasmcloud/wash)
* Added a fully functioning REPL to the `wash` CLI
* Provider archives - allows for the bundling of multiple OS/CPU native libraries into a single archive
* **OCI** support - You can now deploy actors and capability providers into a host/lattice from an OCI registry
* First-party capability providers and sample actors are all available in our OCI registry `wasmcloud.azurecr.io`
* `wasmcloud` and `wash` are now installable via package cloud, homebrew, and other convenient methods

{{</ changelog >}}

{{< changelog "changed" >}}

* Completely rewrote the `wasmcloud-host` crate to allow for easier maintenance and adding features
* The [wasmcloud-host](https://crates.io/crates/wasmcloud-host) crate is now entirely `async` and based on the `actix` framework
* Too many others to list here...

{{</ changelog >}}

<hr/>
<!-- {{< changelog "added" >}}
{{</ changelog >}} -->

<!-- {{< changelog "changed" >}}
{{</ changelog >}} -->

<!-- {{< changelog "Removed" >}}
{{</ changelog >}} -->

<!-- {{< changelog "Fixed" >}}
{{</ changelog >}} -->

<!-- {{< changelog "Security" >}}
{{</ changelog >}} -->

<!-- {{< changelog "Unreleased" >}}
{{</ changelog >}} -->