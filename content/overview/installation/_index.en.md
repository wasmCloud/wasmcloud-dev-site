---
title: "Installation"
date: 2018-12-29T11:02:05+06:00
weight: 1
nextPage: "content/overview/getting-started/_index.en.md"
draft: false
---

There are three primary components of a wasmCloud installation:

- The wasmCloud Shell (`wash`)
- The host runtime
- [NATS](https://nats.io) server (v2.7.2 or later)

We will guide you through installation of these components and the other prerequisites.

### Prerequisites

First, let's make sure we have the necessary prerequisites for developing with wasmCloud:

- **Rust SDK**. While other languages will be supported in the future, actor and capability development are supported _Rust-first_, so you will also want to [install Rust](https://www.rust-lang.org/tools/install) if you haven't already.

- **Unix utilities** if you are on a non-linux platform, you will want a package manager such as homebrew (mac) or chocolatey (windows) that can install common utilities. Using your favorite package manager, make sure that you have these installed: **bash**, **git**, **jq**, and **make**

  - We recommend gnu make version 4.3 or later. (for Mac users: you'll need to add the homebrew-installed make to your path _before_ the one in `/usr/bin`).

- [**Docker**](https://docs.docker.com/get-docker/) provides an easy way to start all the services you need for a development environment: NATS, a local OCI registry. We recommend using the newest version of Docker available on your machine, or at least a version that supports [Docker Compose v2](https://docs.docker.com/compose/cli-command/).

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
{{% tab "Rust" %}}

```bash
cargo install wash-cli
```

{{% /tab %}}
{{% tab "Windows" %}}

```bash
choco install wash
```

{{% /tab %}}
{{% /tabs %}}

### Installing NATS and the wasmCloud host runtime

You can install NATS and the wasmCloud host runtime with or without docker. If you have docker installed, the procedure for [installing with docker](./install-with-docker/) is slightly shorter than the procedure below.

#### Install NATS server

NATS is a powerful, cloud native message broker that aims to provide a universal communications substrate across all kinds of workloads. NATS is such a resilient, fast, small, flexible tool that it is part of the core infrastructure requirements of the wasmCloud host. For information on how to install and start the NATS server, please check the [NATS Documentation](https://docs.nats.io/nats-server/installation). Note that we require a version of NATS new enough to contain the embedded _JetStream_ functionality.

Installing NATS is quick and easy. Once it's installed, run it with JetStream enabled:

```bash
nats-server --jetstream
```

#### Install and start the wasmCloud host runtime

Before you start the wasmCloud host runtime, NATS should already be running.

The preferred way to install the wasmCloud host runtime is to download the latest [release](https://github.com/wasmCloud/wasmcloud-otp/releases). Download the `.tar.gz` file into a directory where you want to install the server.

If you are on a Mac, you'll need to run one more command before extracting the release. This command makes it so Gatekeeper will not quarantine parts of the host when you run it:

```bash
sudo xattr -d com.apple.quarantine x86_64-macos.tar.gz
```

Once you have downloaded the release (and run the above command if on a Mac), extract the release using the following command: (replace _ARCH_ with your architecture, and _OS_ with your operating system)

```bash
tar xzf ARCH-OS.tar.gz
```

After extracting from the tar file, the host is fully installed, and the tar file can be deleted.

There are a variety of ways to run the host. To view the command-line options, type `bin/wasmcloud_host`. The quickest way to start the host, so you can move on to [getting started](/overview/getting-started/), is the first option below:

- To start the host running in the current terminal (The host continues running until you ctrl-c or close the terminal window)

  ```bash
  bin/wasmcloud_host foreground
  ```

- Alternately, you can start it in the background with

  ```bash
  bin/wasmcloud_host start
  ```

  and stop it with `bin/wasmcloud_host stop`. You can view the logs with

  ```bash
  tail var/log/erlang.log.1
  ```

- If you're already familiar with Elixir and **iex**, Elixir's interactive shell, and want to dive into the host's internals, execute Elixir statements, and set breakpoints, start the host with

  ```bash
  bin/wasmcloud_host console
  ```

Once the wasmCloud host has started, go ahead to [Getting started](/overview/getting-started/).

#### Install from source

The [wasmCloud OTP host runtime](https://github.com/wasmCloud/wasmcloud-otp) and [wash](https://github.com/wasmcloud/wash) repositories are open source on GitHub. Both of these repositories can be cloned and built locally directly. Consult the README file in the respective repository for prerequisites and instructions for compiling and running.

### Stopping the wasmCloud Host Runtime

There are a number of ways you can stop the runtime, and the recommendation varies with how you started the host. For details, see the [safe shutdown](/reference/host-runtime/safeshutdown) section of the reference guide.
