---
title: "Installation"
date: 2018-12-29T11:02:05+06:00
weight: 1
nextPage: "content/overview/getting-started/_index.en.md"
draft: false
---

There are three primary components of a wasmCloud installation:

- The wasmCloud shell, `wash`
- The wasmCloud host runtime
- [NATS](https://nats.io) server (v2.7.2 or later)

We will guide you through installation of these components and the other prerequisites.

### Prerequisites

First, let's make sure we have the necessary prerequisites for developing with wasmCloud..

- **Language SDKs**: wasmCloud supports [TinyGo](https://tinygo.org/getting-started/install/) and [Rust](https://www.rust-lang.org/tools/install) for actor development and [Rust](https://www.rust-lang.org/tools/install) for capability provider development. Ensure you have a toolchain installed for your preference of language, for now we recommend installing Rust regardless and 

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
{{% /tabs %}}

### Install NATS server

NATS is a powerful, cloud native message broker that aims to provide a universal communications substrate across all kinds of workloads. NATS is such a resilient, fast, small, flexible tool that it is part of the core infrastructure requirements of the wasmCloud host. For information on how to install and start the NATS server, please check the [NATS Documentation](https://docs.nats.io/nats-server/installation). Note that we require a version of NATS new enough to contain the embedded _JetStream_ functionality.

Installing NATS is quick and easy. Once it's installed, run it with JetStream enabled (you'll know you did it correctly when you see some sweet terminal ASCII art):

```plain
nats-server --jetstream
...
... Starting JetStream
...     _ ___ _____ ___ _____ ___ ___   _   __  __
...  _ | | __|_   _/ __|_   _| _ \ __| /_\ |  \/  |
... | || | _|  | | \__ \ | | |   / _| / _ \| |\/| |
...  \__/|___| |_| |___/ |_| |_|_\___/_/ \_\_|  |_|
...
```

### Install and start the wasmCloud host runtime

The preferred way to install the wasmCloud host runtime is to download the latest [release](https://github.com/wasmCloud/wasmcloud-otp/releases). Follow the instructions below for your platform to download and extract wasmCloud.


{{% tabs %}}
{{% tab "x86_64 Linux" %}}

```bash
wget https://github.com/wasmCloud/wasmcloud-otp/releases/download/v0.54.6/x86_64-linux.tar.gz
mkdir -p wasmcloud
tar -xvf x86_64-linux.tar.gz -C wasmcloud
```

{{% /tab %}}
{{% tab "arm64 Linux" %}}

```bash
wget https://github.com/wasmCloud/wasmcloud-otp/releases/download/v0.54.6/aarch64-linux.tar.gz
mkdir -p wasmcloud
tar -xvf aarch64-linux.tar.gz -C wasmcloud
```

{{% /tab %}}
{{% tab "Intel Mac" %}}

```bash
wget https://github.com/wasmCloud/wasmcloud-otp/releases/download/v0.54.6/x86_64-macos.tar.gz
mkdir -p wasmcloud
# This command makes it so the MacOS Gatekeeper will not quarantine parts of the host when you run it:
sudo xattr -d com.apple.quarantine x86_64-macos.tar.gz
tar -xvf x86_64-macos.tar.gz -C wasmcloud
```

{{% /tab %}}
{{% tab "M1 Mac" %}}

```bash
wget https://github.com/wasmCloud/wasmcloud-otp/releases/download/v0.54.6/aarch64-macos.tar.gz
mkdir -p wasmcloud
# This command makes it so the MacOS Gatekeeper will not quarantine parts of the host when you run it:
sudo xattr -d com.apple.quarantine aarch64-macos.tar.gz
tar -xvf aarch64-macos.tar.gz -C wasmcloud
```

{{% /tab %}}
{{% tab "Windows" %}}

```powershell
wget https://github.com/wasmCloud/wasmcloud-otp/releases/download/v0.54.6/x86_64-windows.tar.gz
mkdir wasmcloud
tar -xvf x86_64-windows.tar.gz -C wasmcloud
```

{{% /tab %}}
{{% /tabs %}}

After extracting from the tar file, the host is fully installed, and the tar file can be deleted. If you haven't already, run NATS with Jetstream following the instructions [above](#install-nats-server).

There are a variety of ways to run the host that are described in more detail in [Running the Host](/reference/host-runtime/running). For now, go ahead and `cd wasmcloud` to get into the correct directory and then run:
```bash
bin/wasmcloud_host foreground
```
This will start the host with attached logs and can be exited at any time by doing `ctrl-c`. Now, you're ready to proceed onto [Getting started](/overview/getting-started/).

#### Install from source
The [wasmCloud OTP host runtime](https://github.com/wasmCloud/wasmcloud-otp) and [wash](https://github.com/wasmcloud/wash) repositories are open source on GitHub. Both of these repositories can be cloned and built locally directly. Consult the README file in the respective repository for prerequisites and instructions for compiling and running.

### Stopping the wasmCloud Host Runtime

There are a number of ways you can stop the runtime, and the recommendation varies with how you started the host. For details, see the [safe shutdown](/reference/host-runtime/safeshutdown) section of the reference guide.
