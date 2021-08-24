---
title: "Installation"
date: 2018-12-29T11:02:05+06:00
weight: 1
draft: false
---

 There are three components to a wasmCloud installation that you'll need:
* [NATS](https://nats.io) server (v2.3.4 or later)
* The wasmCloud Shell (`wash`)
* The Host Runtime

While other languages will be supported in the future, actor and capability development are supported _Rust-first_, so you will also want to [install Rust](https://www.rust-lang.org/tools/install) if you haven't already.

 You will also want to make sure that your operating system has its development tool suite installed, which typically includes things like [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) and **make** (you'll need to look up the instructions for installing this in your environment as they vary). Many operating systems come with both **git** and **make** included.
 
### Install the Package Cloud Repository on Linux
wasmCloud utilizes `packagecloud.io` for our **deb** and **rpm** package builds. If you want to install the `wash` CLI via `apt`, then you'll need to configure the Package Cloud source.

You can find our packagecloud.io page [here](https://packagecloud.io/wasmcloud/core/) for more information.

{{% tabs %}}
   {{% tab "Ubuntu/Debian" %}}
    curl -s https://packagecloud.io/install/repositories/wasmcloud/core/script.deb.sh | sudo bash
   {{% /tab %}}
   {{% tab "Fedora" %}}
    curl -s https://packagecloud.io/install/repositories/wasmcloud/core/script.rpm.sh | sudo bash
   {{% /tab %}}
{{% /tabs %}}

### Install wash
The preferred installation method on Linux is via `apt`, while MacOS uses `brew` and windows currently requires a manuall build-and-install step via Rust's `cargo` tool.

{{% tabs %}}
   {{% tab "Ubuntu/Debian" %}}
    sudo apt install wash
   {{% /tab %}}
   {{% tab "Fedora" %}}
    sudo dnf install wash
   {{% /tab %}}
   {{% tab "snap" %}}    
    snap install wash --devmode --edge
   {{% /tab %}}
   {{% tab "MacOS" %}}
    brew tap wasmcloud/wasmcloud
    brew install wash
   {{% /tab %}}
   {{% tab "Rust" %}}
    cargo install wash-cli
   {{% /tab %}}
   {{% tab "Windows" %}}
    cargo install wash-cli
   {{% /tab %}}
{{% /tabs %}}

### Install NATS Server
NATS is a powerful, cloud native message broker that aims to provide a universal communications substrate across all kinds of workloads. NATS is such a resilient, fast, small, flexible tool that it is part of the core infrastructure requirements of the wasmCloud host. For information on how to install and start the NATS server, please check the [NATS Documentation](https://docs.nats.io/nats-server/installation). Note that we require a version of NATS new enough to contain the embedded _JetStream_ functionality.

Installing NATS is quick and easy.

### Install the wasmCloud Host Runtime
The preferred way to install the wasmCloud host runtime is to download one of the [releases](https://github.com/wasmCloud/wasmcloud-otp/releases), which are `.tar.gz` files containing an [OTP](https://www.erlang.org/) release. Open the releases page, download the latest archive, and then uncompress it into whatever directory from which you plan to run the wasmCloud host.

There is no "installer", so the release runs from wherever you extract the archive.

Once you've extracted the archive contents into a directory, there are a number of different ways you can start the host runtime. Run the following command in your terminal prompt from the release's root directory to see the options:

```shell
$./bin/wasmcloud_host

USAGE
  wasmcloud_host <task> [options] [args..]

COMMANDS

  start                Start wasmcloud_host as a daemon
  start_boot <file>    Start wasmcloud_host as a daemon, but supply a custom .boot file
  foreground           Start wasmcloud_host in the foreground
  console              Start wasmcloud_host with a console attached
  console_clean        Start a console with code paths set but no apps loaded/started
  console_boot <file>  Start wasmcloud_host with a console attached, but supply a custom .boot file
  stop                 Stop the wasmcloud_host daemon
  restart              Restart the wasmcloud_host daemon without shutting down the VM
  reboot               Restart the wasmcloud_host daemon
  upgrade <version>    Upgrade wasmcloud_host to <version>
  downgrade <version>  Downgrade wasmcloud_host to <version>
  attach               Attach the current TTY to wasmcloud_host''s console
  remote_console       Remote shell to wasmcloud_host''s console
  reload_config        Reload the current system''s configuration from disk
  pid                  Get the pid of the running wasmcloud_host instance
  ping                 Checks if wasmcloud_host is running, pong is returned if successful
  pingpeer <peer>      Check if a peer node is running, pong is returned if successful
  escript              Execute an escript
  rpc                  Execute Elixir code on the running node
  eval                 Execute Elixir code locally
  describe             Print useful information about the wasmcloud_host release

No custom commands found.

Use wasmcloud_host help <task> to get more information about a particular task (except custom commands)
```
If you just want to get moving with the [getting started](../getting-started) guide, then you can run `./bin/wasmcloud_host start` and it will start as a background process/daemon. If you're already familiar with Elixir and you know **iex**, then `./bin/wasmcloud_host console` is a good way to roll your sleeves up and get right to work. 

Just remember that if you use the `foreground` option, it will block your terminal window/tab and you'll need to open another one.

If you want to get metadata about the wasmCloud host release, then you can run `./bin/wasmcloud_host describe` and you'll see the version number, the [ERTS](https://erlang.org/doc/apps/erts/index.html) version, configuration file locations, etc.

### Run using Docker
If you don't want to download and install a wasmCloud host release archive, you can simply start the wasmCloud host process using the [docker image](https://hub.docker.com/r/wasmcloud/wasmcloud), e.g.

```shell
docker run wasmcloud/wasmcloud_host:latest
```

### Install from Source
The [wasmCloud OTP host runtime](https://github.com/wasmCloud/wasmcloud-otp) and [wash](https://github.com/wasmcloud/wash) repositories are open source on GitHub. Both of these repositories can be cloned and built locally directly. Consult the README file in the respective repositories for information and pre-requisites on compilation.