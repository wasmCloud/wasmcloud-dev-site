---
title: "Install with docker"
date: 2018-12-29T11:02:05+06:00
weight: 1
draft: false
---

Let's install NATS and the wasmCloud host runtime with docker. You should have already installed [prerequisites and wash](/overview/installation/).


Download the [sample docker-compose file](https://raw.githubusercontent.com/wasmCloud/examples/main/docker/docker-compose.yml) and put it into your work directory. This compose file will run nats, a local OCI registry, a redis container, and the `wasmcloud_host` container. In this format it's easy to run all the necessary services for a wasmCloud host with only a docker installation.

#### Starting the wasmCloud host with docker

With the `docker-compose.yml` file in the current directory, start the processes with
```
docker-compose up
```

The host will run until you type ctrl-c or close the terminal window. To start the docker-compose process in the background, add a `-d` flag:

```
docker-compose up -d
```

If the wasmCloud host is running in docker in the background, you can view its logs (live) with 

```
docker logs -f wasmcloud
```


That's it! Let's move on to [Getting started](/overview/getting-started/)

