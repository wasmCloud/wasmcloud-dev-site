---
title: "Testing the New Provider"
date: 2018-12-29T11:02:05+06:00
weight: 10
draft: false
---

There are two ways to test a capability provider.

The first method loads the provider into a test harness, and issues rpc calls to it to verify responses. This type of testing can be useful for "unit" tests - evaluating the provider in an isolated, controlled context. For examples of providers that use this kind of testing, look in the `tests` folder of [kvredis](https://github.com/wasmCloud/capability-providers/tree/main/kvredis) and [httpserver](https://github.com/wasmCloud/capability-providers/tree/main/httpserver-rs). These two capability providers exhibit different directions for rpc: kvredis, an example of a [`providerReceive`](/interfaces/traits/#wasmbus) service, receives commands from actors, and httpserver, an example of an [`actorReceive`](/interfaces/traits/#wasmbus) service, sends messages to actors.

A second method for testing a provider is to run it in a realistic environment, interacting with a real host and real actors. To set up the environment,

1. Start up a new local OCI registry. You can download [docker-compose.yml](https://github.com/wasmCloud/examples/blob/main/docker/docker-compose.yml) and run `docker-compose up -d registry`

2. Upload the newly-created _provider archive_ to the local OCI registry (You can use `wash reg push ...`, or if you have one of the provider project Makefiles, `make push`)

3. Upload an actor that utilizes the provider to the local OCI registry (`make` and `make push` from the actor source folder to compile it, sign it, and push it to the registry), An example of doing this can be found in the [start the actor](../../create-actor/run/#launch-the-actor) section. _Important_: for an actor to communicate with a provider, it must be signed with the provider's capability contract id, _and_ the actor and provider must be linked. Linking can be performed [on the command line](/app-dev/create-actor/run/#add_a_link_definition) or via the web UI.

4. Use `wash ctl link` from the CLI or use the dashboard web UI to establish a link between the actor and the provider. For the fakepay provider, no extra configuration parameters values are required for the link command. Note that even if you don't supply configuration values, an actor must be linked to a provider, and be signed with sufficient claims, before it can communicate with the provider.

6. Use `wash call actor -o json --data input.json` or the dashboard UI to send a JSON payload (in the file `input.json`) that matches whatever inbound request your actor uses to trigger the payment provider. In the previous section we used `CheckoutRequest` that might have a JSON body as follows:

    ```json
    {
        "payment_entity": "ab3428cj2q34kdas23j0123_123",
        "payment_token": "token1"
    }
    ```

7. Since the response will be serialized in _message pack_ format, the data may look a little awkward in the terminal output, but you'll be able to see your actor using your new provider in the output.
