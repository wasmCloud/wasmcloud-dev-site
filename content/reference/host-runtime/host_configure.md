---
title: "Host Configuration"
date: 2021-08-18T11:02:05+06:00
weight: 10
draft: false
---

Host runtime configuration is currently done through environment variables. These variables can either come from `.env` files or through real environment variables. Real variables take precedence over `.env` files. 

The following sources of environment variables will be considered:

* `.env`
* `.env.local`
* `.env.[dev|test|prod]`
* `.env.[dev|test|prod].local`

As mentioned, any environment variable supplied at runtime will override variables supplied in any `.env` file. Best practice suggests that developers use the `.local` files to represent their local workstation environments and to not check those files into source control.

The following table contains a list of all supported environment variables.

| Variable<br/>_Description_ | Default |
| :--- | :--- | :--- |
| `WASMCLOUD_HOST_KEY`<br/>A 56-character public key used to identify the host| `{runtime generated}` |
| `WASMCLOUD_HOST_SEED`<br/> A 56-character seed key corresponding to the host public key | `{runtime generated}` |
| `WASMCLOUD_LATTICE_PREFIX`<br/>The prefix used to isolate multiple lattices from each other within the same NATS topic space | `default` |
| `WASMCLOUD_RPC_HOST`<br/>NATS server host used for the RPC connection | `0.0.0.0` |
| `WASMCLOUD_RPC_PORT`<br/>NATS server port used for the RPC connection | `4222` |
| `WASMCLOUD_RPC_SEED`<br/>If decentalized NATS auth is used, the user seed | `""` | 
| `WASMCLOUD_RPC_JWT`<br/>If decentalized NATS auth is used, the user JWT | `""` | 
| `WASMCLOUD_RPC_TIMEOUT_MS`<br/>Timeout in milliseconds for RPC calls and heartbeat checks | `2000` |
| `WASMCLOUD_CTL_HOST`<br/>NATS server host used for the _control interface_ connection | `0.0.0.0` |
| `WASMCLOUD_CTL_PORT`<br/>NATS server port used for the _control interface_ connection | `4222` | 
| `WASMCLOUD_CTL_SEED`<br/>If decentalized NATS auth is used, the user seed for the _control interface_ connection | `""` |
| `WASMCLOUD_CTL_JWT`<br/>If decentalized NATS auth is used, the user JWT for the _control interface_ connection | `""` | 
| `WASMCLOUD_CLUSTER_SEED`<br/>The seed key used by this host to sign all invocations. Note that different hosts can use different seed keys so long as their corresponding public keys are listed in the valid issuers variable. | `{generated}` |
| `WASMCLOUD_CLUSTER_ISSUERS`<br/>A comma-delimited list of valid public keys that can be used as _issuers_ on signed invocations | `{generated}` | 
| `WASMCLOUD_PROV_SHUTDOWN_DELAY_MS`<br/>Delay, in milliseconds, between requesting a provider shut down and forcibly terminating its OTP process | `300` |
| `WASMCLOUD_OCI_ALLOW_LATEST`<br/>Determines whether OCI images tagged `latest` are allowed to be pulled and started. Defaults to false because `latest` is a possible attack and instability vector | `false` |
| `WASMCLOUD_OCI_ALLOWED_INSECURE`<br/>The list of OCI hosts to which insecure connections are allowed. By default, no insecure connections are allowed. | `""` |


