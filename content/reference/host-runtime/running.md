---
title: "Running the Host"
date: 2018-12-29T11:02:05+06:00
weight: 11
draft: false
---

The wasmCloud host runtime is an [Elixir distillery release](https://elixirschool.com/en/lessons/misc/distillery#what-is-a-release-0) that includes various scripts for running and managing an application. By running `bin/wasmcloud_host`, you'll see a variety of commands and options:
```plain
./bin/wasmcloud_host
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
  attach               Attach the current TTY to wasmcloud_host's console
  remote_console       Remote shell to wasmcloud_host's console
  reload_config        Reload the current system's configuration from disk
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
There are a variety of commands and options to get used to here, we generally only focus on the commands that manage starting, stopping, and debugging a wasmCloud application.

- To start the host running in the current terminal, which is recommended to easily view logs, you can use `foreground`

  ```bash
  bin/wasmcloud_host foreground
  ```

- Alternately, you can start it in the background as a daemon with `start`

  ```bash
  bin/wasmcloud_host start
  ```

  and stop it with
  ```bash
  bin/wasmcloud_host stop
  ```
  or restart it with
  ```bash
  bin/wasmcloud_host reboot
  ```
  
  If you choose this option, host logs will be located under `var/log` and can be viewed with:

  ```bash
  tail var/log/erlang.log.1
  ```

- If you're already familiar with Elixir and **iex**, Elixir's interactive shell, and want to dive into the host's internals, execute Elixir statements, and set breakpoints, start the host including an interactive console with:

  ```bash
  bin/wasmcloud_host console
  ```