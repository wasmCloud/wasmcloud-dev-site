---
title: "Run the Actor"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

In this guide we're going to start the actor "the long way" so that you can get a feel for all of the moving parts of the process. Our tooling documentation should help you get actors started more easily, once you've been through this guide.

### Pre-Requisites
To run this part of the guide, you'll need `wash`. If you don't have `wash`, installation steps are covered on [the installation page](../../../overview/installation)).


### Build the Actor

Building the actor is as easy as running `make` from the terminal prompt while in the project's root directory. This will compile the actor and sign it with a freshly generated seed key. The `Makefile` that came with the newly scaffolded project includes the `wasmcloud:httpserver` claim, so your actor should work right away.

### Launch the Actor

There are countless ways to run the actor we just created, and if you went through the [getting started](../../../overview/getting-started) section, then you'll have seen some of this before. Assuming you have your wasmCloud host runtime running and the web dashboard is on port `4000`, you can open [http://localhost:4000](http://localhost:4000) to view the dashboard.

At this point, simply click **Start Actor** and then choose _from file_. Open a browser to your current project directory and then navigate to the `build` directory. Here you'll find a `(project)_s.wasm` file, where `(project)` is `hello`, or the name you selected for the project. The `_s` suffix indicates that this WebAssembly module has been _signed_.

A moment later you should see the actor in your web UI as shown in the following screenshot:

TODO screenshot actor started

### Start the Web Server

We know our new actor needs a web server, so let's start the HTTP server capability provider. To do that, click the **Start Provider** button and enter this OCI URL:  `wasmcloud.azurecr.io/httpserver:0.13.0`. Now we should have both an actor and a provider, and the dashboard should look like this:

TODO screenshot actor and provider

### Add a link definition

With both the provider and the actor running, the next step is to _link_ the two. This provides a set of configuration values that is unique to one actor's use of a provider. To change things up slightly, and so you develop some muscle memory with the command line tooling, we'll use the `wash` CLI here. To link your actor, you'll' need the actor's public key. You can get that simply by clicking on the clipboard icon next to the actor name in the dashboard web UI (it'll be a long string that starts with **M**).

Once you've got the actor's public key, you can enter the following command, replacing `ACTOR_MODULE_KEY` with the key you just put on your clipboard: 

```
wash ctl link ACTOR_MODULE_KEY wasmcloud.azurecr.io/httpserver:0.13.0 wasmcloud:httpserver 'config_json={"address":"127.0.0.1:7800"}'

```

At this point your HTTP server capability provider has been notified that a link definition was created, and it started the corresponding web server on port `7000`. You can now hit that endpoint and exercise the code you just wrote:

```
curl localhost:7000
```
and you should get the response:

```
Hello World
```

The actor accepts an optional parameter `name`, and uses it to change the greeting. (Notice the quotes around the url below).

```
curl "localhost:7000?name=Carol"
```

The response should be

```
Hello Carol
```


_**Congratulations!**_ You've successfully created and run your first actor. Welcome to the world of boilerplate-free, simple distributed application development in the cloud, browser, and everywhere in between.

