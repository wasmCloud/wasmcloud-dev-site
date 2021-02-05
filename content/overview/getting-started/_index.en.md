---
title: "Getting Started"
date: 2018-12-29T11:02:05+06:00
weight: 2
draft: false
---

In this guide, we'll be covering the following starting actions for wasmCloud development:
1. Launching a wasmCloud Host
1. Starting a wasmCloud Actor
1. Starting a wasmCloud Capability Provider
1. Linking an Actor to a Capability Provider
1. Making requests to an Actor

### Prerequisites
In order to follow this guide, you'll need a few things:
- `wash` (installation covered on [the previous page](../installation))
- [nats-server](https://docs.nats.io/nats-server/installation) **OR** [docker](https://docs.docker.com/engine/install/), recommended with [docker-compose](https://docs.docker.com/compose/install/)

### Starting NATS
[NATS](https://nats.io/) is a reliable cross-environment messaging system that wasmCloud uses extensively to send messages between the core program components. wasmCloud _can_ be run without using NATS, however this is only recommended in extremely constrained environments, or when NATS is unavailable.

In a terminal window, use one of the following options to launch `nats-server` locally. We recommend using the `docker-compose` as it will also launch services that are used in other places in this documentation.

{{% tabs %}}
   {{% tab "docker-compose" %}}
    # This file is located in the wash repository under tools/
    cat <<EOF > docker-compose.yml
    version: "3"
    services:
      registry:
        image: registry:2
        ports:
        - "5000:5000"
      nats:
        image: nats:2.1.9
        ports:
        - "6222:6222"
        - "4222:4222"
        - "8222:8222"
      redis:
        image: redis:6.0.9
        ports:
        - "6379:6379"   
    EOF
    docker-compose up
   {{% /tab %}}
   {{% tab "docker" %}}
    docker run -p 4222:4222 -ti nats:latest
   {{% /tab %}}
   {{% tab "nats-server binary" %}}
    nats-server -a 0.0.0.0 -p 4222
   {{% /tab %}}
 {{% /tabs %}}

### Running your first wasmCloud Host
In a separate terminal window, run the following command to launch an interactive wasmCloud REPL environment, including a preconfigured wasmCloud host.
```shell
wash up
```
At any time, you can type `help` to see a printed list of subcommands in the log output window in the bottom-right pane. To exit the repl, you can type `exit`, `logout`, `q` or `:q!`.
![REPL](../wash_up.png)

To see a list of running hosts, run the following command in the REPL:
```shell
wash> ctl get hosts
```
You should see one host running, and you can view its inventory by running the following command with the **Host ID** found in the Output window:
```shell
wash> ctl get inventory NXYZABC...
```
You'll see output similar to the following (public key output truncated for documentation):
```shell
               Host Inventory (NBMRMKELSYA645ZBGN6AEYJU6GV...)                
                                                                                                        
hostcore.os                                                      macos                                        
hostcore.osfamily                                                unix                                         
hostcore.arch                                                    x86_64                                       
repl_mode                                                        true                                         
                          No actors found                                             
                                                                                                        
Provider ID               Link Name                  Image Reference   
VDHPKGFKDI34Y4RN4PWWZH... default                    N/A               
VAHNM37G4ARHZ3CYHB3L34... __wasmcloud_lattice_cache  N/A               
```
Currently on this Host, we have a few labels that show the environment this Host is running on, and two default providers. The first, starting with `VDHPK`, is the `extras` provider, which provides an interface for wasmCloud resources to generate random numbers, GUIDS, etc. The second, starting with `VAHNM`, is the lattice cache provider, which provides a distributed cache to all hosts running in this lattice. We'll talk more about what a **Lattice** is in the [Platform Builder](../../platform-builder) section of the documentation.

#### Running an Actor
We can start scheduling Actors and Providers right away on this Host using the `ctl start` command. To begin, we'll start our `Echo` sample actor.
```shell
wash> ctl start actor wasmcloud.azurecr.io/echo:0.1.0
```
The `Echo` sample actor has a single operation `HandleRequest`, which will respond to a delivered HTTP Request with an identical response that "echoes" the request sent.

#### Running a Capability Provider
In order for this actor to receive HTTP requests, we're going to need to start the HTTP Server Capability Provider. Actors are signed WebAssembly modules that do not have access to create or receive HTTP requests, so the capability provider will do that for us and deliver messages to and from the actor. 
```shell
wash> ctl start provider wasmcloud.azurecr.io/httpserver:0.10.0
```
Let's take a look at our host's inventory now, you can either type the command above again or use the `UP/DOWN` arrow keys to navigate to the previous command. It should look something like this (some output truncated for documentation):
```shell
               Host Inventory (NBMRMKELSYA645ZB...)                           
                                                                                                                              
hostcore.osfamily                                           unix                                                              
hostcore.os                                                 macos                                                             
hostcore.arch                                               x86_64                                                            
repl_mode                                                   true                                                              
                                                                                                                              
Actor ID       Image Reference                                                   
MBCFOPM6JW2... wasmcloud.azurecr.io/echo:0.1.0                                         
                                
Provider ID    Link Name        Image Reference                        
VDHPKGFKDI3... default          N/A                                    
VAHNM37G4AR... __wasmcloud_...  N/A                                    
VAG3QITQQ2O... default          wasmcloud.azurecr.io/httpserver:0.10.0 
```

#### Linking Actors and Capability Providers
Now your `Echo` Actor and `HTTP Server` Providers are running, but they aren't connected. In order to allow the Actor and Provider to communicate, we need to `link` them together, which we do using their respective Public Keys (shown in the Inventory as `Actor ID` and `Provider ID`).

You can use the following command to `link` your Actor and Provider, on MacOS you might receive a message asking if you want `wash` to receive incoming network connections. You should click "Allow", this is just setting up the provider's local HTTP server to listen on port 8080.
```shell
wash> ctl link MBCFOPM6JW2APJLXJD3Z5O4CN7CPYJ2B4FTKLJUR5YR5MITIU7HD3WD5 VAG3QITQQ2ODAOWB5TTQSDJ53XK3SHBEIFNK4AYJ5RKAX2UNSCAPHA5M wasmcloud:httpserver PORT=8080
```
Once you see that the link has been advertised, you are ready to send a request to your actor.

#### Interacting with your Actor
In another terminal window (not the REPL), run the following command:
```shell
curl localhost:8080/echo
```
In response, you should receive your request object (notice the path argument):
```shell
{"method":"GET","path":"/echo","query_string":"","headers":{"host":"localhost:8080","user-agent":"curl/7.64.1","accept":"*/*"},"body":[]}
```
Feel free to try out different methods of making a request to your Actor, including adding headers or using a different HTTP method to see different outputs.

Instead of using `curl`, you can also _directly invoke_ actors' registered functions using `ctl call`. As mentioned before, the function that "echoes" this HTTP request is registered with the operation `HandleRequest`, and we can make this request directly to the actor if we supply the correct parameters.

Here's an example of using `call` in the REPL to mimic our above `curl`. Note that this is not interacting with the HTTP server provider, and we don't need it to be running or linked for this operation to succeed:
```shell
wash> ctl call MBCFOPM6JW2APJLXJD3Z5O4CN7CPYJ2B4FTKLJUR5YR5MITIU7HD3WD5 HandleRequest {"method": "GET", "path": "/echo", "body": "", "queryString":"","header":{}}
```
Our output will look something like this:
```shell
Call response (raw): ��statusCode�Ȧstatus�OK�header��body�S{"method":"GET","path":"/echo","query_string":"","headers":{},"body":[]}
```
Because of our method of serialization / deserialization, the return payload has some characters that the terminal doesn't know how to interpret from its bytes.[<sup>1</sup>](#references) However you can still see the response body which contains our exact "echoed" request.

That covers the basics of wasmCloud development in our REPL environment. To learn more about Actors, Providers, and more concepts on wasmCloud, continue onto the [App Development](../../app-dev) or [Platform Building](../../platform-builder) sections depending on your role.

###### References
1. We're using [msgpack](https://msgpack.org/) to serialize arbitrary byte payloads when sending information to/from actors. Due to this serialization format, the bytes that are sent to and from actors must be deserialized with the shape of the object already known, and in the case of `ctl call` we are doing our best guess as to what the return payload is going to look like. As you can see when using `curl`, when we know the shape of the data (an HTTP response) we're able to fully deserialize the actor's response.