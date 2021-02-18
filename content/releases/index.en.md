---
title: "Release History"
description: "This is a high-level overview of the additions, removals, and changes for releases of wasmcloud and related tooling. This is not a complete list. For all of the specific details, check out the release notes for each release in the appropriate repository."
layout: "changelog"
draft: false
---

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

{{< changelog "Removed" >}}
{{</ changelog >}}

{{< changelog "Fixed" >}}
{{</ changelog >}}

{{< changelog "Security" >}}
{{</ changelog >}}

{{< changelog "Unreleased" >}}
{{</ changelog >}}

<hr/>