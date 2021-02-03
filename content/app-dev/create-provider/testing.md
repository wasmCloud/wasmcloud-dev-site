---
title: "Testing the New Provider"
date: 2018-12-29T11:02:05+06:00
weight: 10
draft: false
---

The easiest way to test your new capability provider is as follows:

1. Start up a new local OCI registry (we have an example of these `docker-compose` files in multiple sample repositories)

1. Upload the newly-created _provider archive_ to the local OCI registry (You can use `wash reg push`)

1. Upload an actor that utilizes this provider to the local OCI registry (`wash reg push`)

1. Use `ctl link` inside the `wash up` REPL to establish a link (no values necessary) between the actor and the provider. Note that even if you don't supply configuration values, an actor must be linked to a provider (and have sufficient claims) before it can communicate with it.

1. Use `ctl call` to fabricate a JSON payload that matches whatever inbound request your actor uses to trigger the payment provider. In the previous section we used `CheckoutRequest` that might have a JSON body as follows:

    ```json
    {
        "payment_entity": "ab3428cj2q34kdas23j0123_123",
        "payment_token": "token1"
    }
    ```

1. Since the response will be serialized in _message pack_ format, the data may look a little ugly in the REPL log, but you'll be able to see your actor using your new provider in the output.
