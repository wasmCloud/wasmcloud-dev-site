---
title: "Run the Actor"
date: 2018-12-29T11:02:05+06:00
weight: 5
draft: false
---

In this guide we're going to start the actor "the long way" so that you can get a feel for all of the moving parts of the process. Our tooling documentation should help you get actors started easier once you've been through this guide.

### Pre-Requisites
To run this part of the guide, you'll need a local OCI (Open Container Initiative)-compliant registry. The easiest way to get an OCI registry running locally is with `docker` and it's `registry` image.

### Deploy a Local Docker Registry

The `wash` tooling and the embedded REPL launch actors and capability providers from OCI-compliant registries. One such registry is the docker registry. You can run this locally with the following command:

```
docker run -d -p 5000:5000 --restart=always --name registry registry:2
```

For more information on running the registry, you can read [Docker's documentation](https://docs.docker.com/registry/deploying/).

### Push Your Actor

Next you'll want to push your actor to the local registry. If you haven't configured authentication or any other restrictions, you should be able to use the following `wash` command to push it to the registry:

```
wash reg push --insecure localhost:5000/(image):(tag) ./target/wasm32-unknown-unknown/debug/(actor)_s.wasm
```

For example, you might use `newactor` and `v1` as the image and tag respectively, giving you a local registry URL (you'll need to remember this) of `http://localhost:5000/newactor:v1`. Make sure the path you supply here is to the _signed_ version of your actor's WebAssembly module.

### Launch the Actor

There are countless ways to run the actor we just created, but the easiest is probably to use our **REPL**, by typing `wash up` at the command line. You should see a screen that looks similar to the following:

![Wash Up](../wash_up.png)

Once inside the wash REPL, you can issue this command to start the actor (make sure you change the OCI URL to the one you used in the previous step):

```
ctl start actor localhost:5000/newactor:v1
```

Next, we'll need to start the HTTP Server. Fortunately, the wasmcloud official HTTP server capability provider is published in Azure Container Registry, so you can start it with the following REPL command:

```
ctl start provider wasmcloud.azurecr.io/httpserver:0.12.1
```

With both the provider and the actor running, the only thing left to do is _link_ the two. This provides a set of configuration values that is unique to one actor's use of a provider. Type the following command to link your actor with the provider:

```
ctl link (ACTOR_MODULE_KEY) VAG3QITQQ2ODAOWB5TTQSDJ53XK3SHBEIFNK4AYJ5RKAX2UNSCAPHA5M wasmcloud:httpserver PORT=8080
```

To get your `(ACTOR_MODULE_KEY)`, a 56-character string beginning with the letter **M**, you can check them from inside the REPL by inspecting the local registry:

```
claims inspect --insecure localhost:5000/newactor:v1
```

You should see in the log output that the actor configuration was passed and you should see an HTTP server starting on port 8080. Now you can type the following in a separate terminal prompt to curl your newly started endpoint:

```
curl localhost:8080/testing
{"greeting": "Hello world!"}
```

Congratulations! You've successfully created and run your first actor. Welcome to the world of portable, composable application development in the cloud.