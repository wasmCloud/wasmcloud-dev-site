---
title: "Run the Actor"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

In this guide we're going to start the actor "the long way" so that you can get a feel for all of the moving parts of the process. Our tooling documentation should help you get actors started easier once you've been through this guide.

### Pre-Requisites
To run this part of the guide, you'll need `wash`. If you don't have `wash`, installation steps are covered on [the installation page](../../../overview/installation)).

### Launch the Actor

There are countless ways to run the actor we just created, but the easiest is probably to use our **REPL**, by typing `wash up` at the command line. You should see a screen that looks similar to the following:

![Wash Up](../wash_up.png)

Once inside the wash REPL, you can issue this command to start the actor. Make sure the path you supply here is to the _signed_ version of your actor's WebAssembly module.

```
ctl start actor myactor_s.wasm
```

Next, we'll need to start the HTTP Server. Fortunately, the wasmcloud official HTTP server capability provider is published in Azure Container Registry, so you can start it with the following REPL command:

```
ctl start provider wasmcloud.azurecr.io/httpserver:0.12.1
```

With both the provider and the actor running, the only thing left to do is _link_ the two. This provides a set of configuration values that is unique to one actor's use of a provider. Type the following command to link your actor with the provider:

```
ctl link (ACTOR_MODULE_KEY) VAG3QITQQ2ODAOWB5TTQSDJ53XK3SHBEIFNK4AYJ5RKAX2UNSCAPHA5M wasmcloud:httpserver PORT=8080
```

To get your `(ACTOR_MODULE_KEY)`, a 56-character string beginning with the letter **M**, you can check them from inside the REPL by inspecting the local signed wasm:

```
claims inspect --insecure myactor_s.wasm
```

You should see in the log output that the actor configuration was passed and you should see an HTTP server starting on port 8080. Now you can type the following in a separate terminal prompt to curl your newly started endpoint:

```
curl localhost:8080/testing
{"greeting": "Hello world!"}
```

Congratulations! You've successfully created and run your first actor. Welcome to the world of portable, composable application development in the cloud.