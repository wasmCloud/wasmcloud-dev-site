---
title: "Running in a Docker Image"
date: 2018-12-29T11:02:05+06:00
weight: 2
draft: false
---

Running the wasmCloud host runtime from a docker image is nearly as easy as running it locally. For more information on the docker image and how to use it, please see the official wasmCloud [docker hub organization](https://hub.docker.com/u/wasmcloud).

To pull the docker image:

```
docker pull wasmcloud/wasmcloud:${VERSION}
```

To start the host directly from the docker image:

```
docker run wasmcloud/wasmcloud:${VERSION}
```
