---
title: "Installation"
date: 2018-12-29T11:02:05+06:00
weight: 1
draft: false
---

wasmcloud is a suite of tools and libraries that can be used in a number of different use cases and patterns. The first
thing you will want to install is the **wasmcloud core**, which includes the default host runtime binary and the wasmcloud shell (`wash`) CLI.

If you plan on building capability providers or working within the Rust ecosystem, we highly recommend that you [install Rust](https://www.rust-lang.org/tools/install) in addition to the core.

### (Linux) Install the Package Cloud Repository
wasmcloud utilizes packagecloud.io for our deb and rpm package builds.

You can find our packagecloud.io page [here](https://packagecloud.io/wasmcloud/core/) for more information.

{{% tabs %}}
   {{% tab "Ubuntu/Debian" %}}
    curl -s https://packagecloud.io/install/repositories/wasmcloud/core/script.deb.sh | sudo bash
   {{% /tab %}}
   {{% tab "Fedora" %}}
    curl -s https://packagecloud.io/install/repositories/wasmcloud/core/script.rpm.sh | sudo bash
   {{% /tab %}}
{{% /tabs %}}

#### Install wasmcloud and wash

{{% tabs %}}
   {{% tab "Ubuntu/Debian" %}}
    sudo apt install wasmcloud wash
   {{% /tab %}}
   {{% tab "Fedora" %}}
    sudo dnf install wasmcloud wash
   {{% /tab %}}
   {{% tab "MacOS" %}}
    brew tap wasmcloud/wasmcloud
    brew install wasmcloud wash
   {{% /tab %}}
   {{% tab "All" %}}
    cargo install wasmcloud wash-cli
   {{% /tab %}}
{{% /tabs %}}

⚠️ **M1 Mac Users** - The default WebAssembly engine for wasmCloud, `wasm3`, does not currently support M1 Macs. You can still use `wasmcloud` and `wash` by installing with the `wasmtime` feature flag, shown by the following command:
```
cargo install wasmcloud wash-cli --no-default-features --features wasmtime
```

⚠️ **Windows Users** - Windows is currently only supported when loading capability providers as static plugins. We highly recommend using the [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10) for the best wasmcloud experience.

### (All) Install from Source
The [wasmcloud](https://github.com/wasmcloud/wasmcloud) and [wash](https://github.com/wasmcloud/wash) repositories are open sourced on GitHub. Both of these repositories can be cloned and build locally directly via [cargo](https://doc.rust-lang.org/cargo/), provided you have `Rust` installed.
