---
title: "Installation"
date: 2018-12-29T11:02:05+06:00
weight: 1
draft: false
---

wasmCloud is a suite of tools and libraries that can be used in a number of different use cases and patterns. The first
thing you will want to install is the **wasmCloud core**, which includes the default host runtime binary and the wasmCloud shell (`wash`) CLI.

If you plan on building capability providers or working within the Rust ecosystem, we highly recommend that you [install Rust](https://www.rust-lang.org/tools/install) in addition to the core.

### Install the Package Cloud Repository
wasmCloud utilizes packagecloud.io for our deb and rpm package builds.

You can find our packagecloud.io page [here](https://packagecloud.io/wasmCloud/core/) for more information.

{{% tabs %}}
   {{% tab "Ubuntu/Debian" %}}
    curl -s https://packagecloud.io/install/repositories/wasmCloud/core/script.deb.sh | sudo bash
   {{% /tab %}}
   {{% tab "Fedora" %}}
    curl -s https://packagecloud.io/install/repositories/wasmCloud/core/script.rpm.sh | sudo bash
   {{% /tab %}}
{{% /tabs %}}

### Install wasmCloud and wash

{{% tabs %}}
   {{% tab "Ubuntu/Debian" %}}
    sudo apt install wasmcloud wash
   {{% /tab %}}
   {{% tab "Fedora" %}}
    dnf install wasmcloud wash
   {{% /tab %}}
   {{% tab "MacOS" %}}
    brew tap wasmcloud/wasmcloud
    brew install wasmcloud wash
   {{% /tab %}}
{{% /tabs %}}

