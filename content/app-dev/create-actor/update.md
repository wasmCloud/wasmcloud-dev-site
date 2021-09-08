---
title: "Customizing the actor"
date: 2018-12-29T11:02:05+06:00
weight: 4
draft: false
---

Our new actor has come pre-equipped with a message handler that generates a text string in the body of the response. By default, the text is "Hello World", and the greeting changes if the url contains a name parameter. We will modify the actor to select the greeting, using a second url paramter. In this exercise, you will go through the process of editing code, recompiling, and updating the actor in a live running system.

We are going to modify the business logic to change the greeting based on a second parameter, `msg`. If msg=hello (or if there is no msg parameter), the greeting will be "Hello NAME". If msg=bye, the greeting will be 'Goodbye NAME', and so on.  

```rust
    async fn handle_request(
        &self,
        _ctx: &Context,
        req: &HttpRequest,
    ) -> std::result::Result<HttpResponse, RpcError> {
        let name = form_urlencoded::parse(req.query_string.as_bytes())
            .find(|(n, _)| n == "name")
            .map(|(_, v)| v.to_string())
            .unwrap_or_else(|| "You".to_string());

        let msg_id = form_urlencoded::parse(req.query_string.as_bytes())
            .find(|(n, _)| n == "msg")
            .map(|(_, v)| v.to_string())
            .unwrap_or_else(|| "hello".to_string());

        let response = match msg_id.as_str() {
            "hello" => format!("Hello {}", name),
            "bye" => format!("Goodbye {}", name),
            "hey" => format!("Hey {}, what's up?", name),
            _ => format!("I didn't  understand that, {}", name),
        };

        Ok(HttpResponse {
            body: response.as_bytes().to_vec(),
            ..Default::default()
        })
    }
```

In `src/lib.rs`, replace handle_request with the code above. Before rebuilding, we need to increment the version numbers.

In `Cargo.toml`, change `version = "0.1.0"` to `version = "0.1.1"`. 
In `Makefile`, change `REVISION=0` to `REVISION=1`.

Then run `make` to build the actor and re-sign it  If there are no errors compiling the actor, you are ready to update it on the host. Earlier in this guide, we started the actor from the web ui. This time we'll update it from the command line.

There are `wash` commands to push the signed webassembly file to the local registry, and to issue a command to the host to load the actor from the registry and update it. We've written commands in the makefile for you to fill in the arguments for the wash commands, so all you need to do is type these two commands:

```shell
make push
make update
```

It is not necessary to re-issue a link command. The host replaces all running instances of the actor with the new version, and re-links them with the HttpServer capability provider. 


Let's try out the new actor:

```shell
curl 'localhost:7000/?name=Alice,msg=hello'
curl 'localhost:7000/?name=Bob,msg=bye'
curl 'localhost:7000/?name=Carol,msg=hey'

```

You've just completed a full development cycle of test, edit, recompile, run - all while the host continued running. If an actor is handling a message when an update occurs, the host waits for the message handler to complete, then swaps in the newer webassembly module before the next message is processed. 

_This is the way production software services were meant to be_. 

No-fuss version management. Secure software supply-chain.
Zero-downtime upgrades.
