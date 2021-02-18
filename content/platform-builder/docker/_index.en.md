---
title: "Running in a Docker Image"
date: 2018-12-29T11:02:05+06:00
weight: 2
draft: false
---

Running the `wasmcloud` binary in a docker image is nearly as easy as running it locally. For more information on the docker image and how to use it, please see the official [docker hub organization](https://hub.docker.com/u/wasmcloud) for wasmcloud.

To pull the docker image:

```
docker pull wasmcloud/wasmcloud:${VERSION}
```

To run wasmcloud directly from the docker image:

```
docker run wasmcloud/wasmcloud:${VERSION}
```
