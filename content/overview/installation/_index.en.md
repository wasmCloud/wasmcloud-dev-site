---
title: "Installation"
date: 2018-12-29T11:02:05+06:00
weight: 1
nextPage: "content/overview/getting-started/_index.en.md"
draft: false
---

We will guide you through installation of `wash`, the wasmCloud Shell, which gives you the tools to install, run, and develop with wasmCloud.

### Prerequisites

Other than `wash`, prerequisites generally center around the language toolchain of your choice or your preferred way to run wasmCloud.

- **Language SDKs**: wasmCloud supports [TinyGo](https://tinygo.org/getting-started/install/) and [Rust](https://www.rust-lang.org/tools/install) for actor development and [Rust](https://www.rust-lang.org/tools/install) for capability provider development. Ensure you have a toolchain installed for your preference of language.

- **Unix utilities**: We include Makefiles with our project templates to simplify development that assume some common command line utilities. Using your favorite package manager, make sure that you have these installed: **bash**, **git**, **jq**, and **make**
  - We recommend gnu make version 4.3 or later. (for Mac users: you'll need to add the homebrew-installed make to your path _before_ the one in `/usr/bin`).

> If you prefer to use Docker and not install anything locally, you can follow our [install with Docker](./install-with-docker/) instructions instead of the ones below. You'll still want to have `wash` as mentioned above for the guides in the documentation.

### Install wash

The wash CLI is the main tool you'll be using for wasmcloud development. Select your platform below to view the applicable installation commands.

{{% tabs %}}
{{% tab "Ubuntu/Debian" %}}

```bash
curl -s https://packagecloud.io/install/repositories/wasmcloud/core/script.deb.sh | sudo bash
sudo apt install wash
```

{{% /tab %}}
{{% tab "Fedora" %}}

```bash
curl -s https://packagecloud.io/install/repositories/wasmcloud/core/script.rpm.sh | sudo bash
sudo dnf install wash
```

{{% /tab %}}
{{% tab "snap" %}}

```bash
snap install wash --devmode --edge
```

{{% /tab %}}
{{% tab "MacOS" %}}

```bash
brew tap wasmcloud/wasmcloud
brew install wash
```

{{% /tab %}}
{{% tab "Windows" %}}

```bash
choco install wash
```

{{% /tab %}}
{{% tab "Rust" %}}

`wash` can be installed with `cargo` in the case that your platform isn't listed
```bash
cargo install wash-cli
```
{{% /tab %}}
{{% tab "Source" %}}

The [wash](https://github.com/wasmcloud/wash) repository is open source on GitHub and can be cloned and built locally directly. With a Rust toolchain, `wash` is easy to build from source
```bash
git clone https://github.com/wasmcloud/wash.git
cd wash
cargo build --release
./target/release/wash
```
{{% /tab %}}
{{% /tabs %}}

Once `wash` is installed, simply run `wash up` . This will start the host with attached logs and can be exited at any time by doing `ctrl-c`. Now, you're ready to proceed onto [Getting started](/overview/getting-started/).

