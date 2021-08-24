---
title: "Run the Actor"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

In this guide we're going to start the actor "the long way" so that you can get a feel for all of the moving parts of the process. Our tooling documentation should help you get actors started easier once you've been through this guide.

### Pre-Requisites
To run this part of the guide, you'll need `wash`. If you don't have `wash`, installation steps are covered on [the installation page](../../../overview/installation)).


### Build the Actor

Building the actor is as easy as running `make` from the terminal prompt while in the project's root directory. This will compile the actor and sign it with a freshly generated seed key. The `Makefile` that came with the newly scaffolded project includes the `wasmcloud:httpserver` claim, so your actor should work right away.

### Launch the Actor

There are countless ways to run the actor we just created, and if you went through the [getting started](../../../overview/getting-started) section, then you'll have seen some of this before. Assuming you have your wasmCloud host runtime running and the web dashboard is on port `4000`, then you can open [http://localhost:4000](http://localhost:4000) to get to the dashboard.

At this point, simply click **Start Actor** and then choose _from file_. Open a browser to your current project directory and then navigate to the `build` directory. Here you'll find a `(project)_s.wasm` file, where `(project)` is the name of the project you created. The `_s` indicates that this WebAssembly module has been _signed_.

A moment later you should see the actor in your web UI as shown in the following screenshot:

TODO screenshot actor started

### Start the Web Server

We know our new actor needs a web server, so let's start the HTTP server capability provider. To do that, click the **Start Provider** button and we can provide the OCI URL here `wasmcloud.azurecr.io/httpserver:0.13.0`. Now we should have both an actor and a provider, as shown here:

TODO screenshot actor and provider

### Add a Link Definition

With both the provider and the actor running, the only thing left to do is _link_ the two. This provides a set of configuration values that is unique to one actor's use of a provider. To change things up slightly, and so you develop some muscle memory with the command line tooling, we'll use the `wash` CLI here. To link your actor, the first thing you need is the actor's public key. You can get that simply by clicking on the clipboard icon next to the actor name in the dashboard web UI (it'll be the key that starts with **M**).

Once you've got the actor's public key, you can issue the following `wash` command where `(ACTOR_MODULE_KEY)` is the key you just put on your clipboard: 

```
wash ctl link (ACTOR_MODULE_KEY) VAG3QITQQ2ODAOWB5TTQSDJ53XK3SHBEIFNK4AYJ5RKAX2UNSCAPHA5M wasmcloud:httpserver PORT=8080
```

At this point your HTTP server capability provider has been notified that a link definition was created, and it started the corresponding web server on port `8080`. You can now hit that endpoint and exercise the code you just wrote:

```
curl localhost:8080/testing
{"greeting": "Hello world!"}
```

_**Congratulations!**_ You've successfully created and run your first actor. Welcome to the world of boilerplate-free, simple distributed application development in the cloud, browser, and everywhere in between..