---
title: "Install with docker"
date: 2018-12-29T11:02:05+06:00
weight: 1
draft: false
---

Let's install NATS and the wasmCloud host runtime with docker. You should have already installed [prerequisites and wash](/overview/installation/).


Download the [sample docker-compose file](https://raw.githubusercontent.com/wasmCloud/examples/main/docker/docker-compose.yml) and put it into your work directory. This compose file will install and run nats, a local OCI registry, and a redis container.

With the `docker-compose.yml` file in the current directory, start the background processes with
```
docker-compose up -d
```

#### Starting the wasmCloud host with docker

To download the wasmCloud host and run it in the current terminal:

```
docker run --network host \
  --env WASMCLOUD_RPC_HOST=0.0.0.0 \
  --env WASMCLOUD_CTL_HOST=0.0.0.0 \
  --name wasmcloud \
  wasmcloud/wasmcloud_host:latest
```

The host will run until you type ctrl-c or close the terminal window. To start the host in the background, add a `-d` flag:

```
docker run --network host -d \
  --env WASMCLOUD_RPC_HOST=0.0.0.0 \
  --env WASMCLOUD_CTL_HOST=0.0.0.0 \
  --name wasmcloud \
  wasmcloud/wasmcloud_host:latest
```

If the wasmCloud host is running in docker in the background, you can view its logs (live) with 

```
docker logs -f wasmcloud
```


That's it! Let's move on to [Getting started](/overview/getting-started/)
