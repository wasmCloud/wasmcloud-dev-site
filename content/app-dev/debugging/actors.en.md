---
title: "Actor Troubleshooting"
date: 2022-01-19T11:02:05+06:00
weight: 2
draft: false
---

Before you follow this section, it's highly recommended to configure your log level to `debug` in order to see the most information when debugging. You can use the [host troubleshooting](../host#changing-log-level) section for instructions on how to do this.

When developing actors there are a few often missed steps that can cause your actor to either fail to run or fail to properly operate. Below we'll walk through a few of these scenarios with accompanying error messages.

### Using an insecure registry
```console
Failed to fetch OCI bytes: error sending request for url (https://localhost:5000/v2/): error trying to connect: record overflow
```

This error can happen when attempting to run an actor from the local docker registry included in our examples. If you see this error, you need to set the `WASMCLOUD_OCI_ALLOWED_INSECURE` variable either in your [host config](../../reference/host-runtime/host_configure#supported-configuration-variables) file or as an environment variable. The proper syntax for this is as follows:
{{% tabs %}}
{{% tab "Unix" %}}

```shell
export WASMCLOUD_OCI_ALLOWED_INSECURE=localhost:5000
# Start your host as you did before, either with `mix` for development or the release tarball script
```

{{% /tab %}}
{{% tab "Windows Powershell" %}}

```powershell
$env:WASMCLOUD_OCI_ALLOWED_INSECURE = localhost:5000 
# Start your host as you did before, either with `mix` for development or the release tarball script
```

{{% /tab %}}
{{% tab "Docker" %}}

It's recommended to modify our [sample Docker Compose file](https://raw.githubusercontent.com/wasmCloud/examples/main/docker/docker-compose.yml) to include this variable on the `wasmcloud_host` container. Like so:
```yaml
  wasmcloud:
    image: wasmcloud/wasmcloud_host:latest
    environment:
      WASMCLOUD_RPC_HOST: nats
      WASMCLOUD_CTL_HOST: nats
      WASMCLOUD_PROV_RPC_HOST: nats
      WASMCLOUD_OCI_ALLOWED_INSECURE: localhost:5000
    ports:
      - "4000:4000"
      - "8080-8089:8080-8089" # Allows exposing examples on ports 8080-8089
```

{{% /tab %}}
{{% /tabs %}}

### Missing Capability Claims
When a wasmCloud actor needs access to a capability provider, we require cryptographically signed claims embedded in that actor. The host runtime operates on a deny-by-default and require claims to be an allow-list to ensure our actors are secure. Adding these capability claims is mostly managed for you with `wash` when you build your actor and the space-separated list of capability claims in the `Makefile` included with any actor project.

This can be illustrated with the `KVCounter` example actor fairly easily. The actor needs to be signed with the `wasmcloud:httpserver` capability to receive HTTP requests, and the `wasmcloud:keyvalue` capability to issue requests to a keyvalue database. Omitting either of these will cause errors due to the inability to receive a request or the inability to issue one, so we'll cover both of those scenarios here.