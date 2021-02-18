---
title: "Actor to Actor Calls"
date: 2018-12-29T11:02:05+06:00
weight: 6
draft: false
---

The ability for one actor to call another actor is critical to being able to create _composable_ actor systems. wasmcloud supports RPC-style communication between actors, even if those actors are running in hosts scattered across disparate infrastructure, connected only via the [lattice](/reference/lattice).

### Identifying Actors

The single most important task when calling other actors is to identify the target actor. wasmcloud supports three ways of identifying another actor:

* OCI Reference
* Public Key (Subject)
* Call Alias

#### OCI Reference

The OCI reference for a running actor is the URL used to launch that actor, assuming it was launched from an OCI registry. There are a number of ways to launch actors _without_ supplying an OCI reference, and so you should be cautious when using OCI references to ensure that each running actor you want to call has a corresponding OCI URL.

Invocations sent to OCI references that don't match a running actor will fail accordingly.

#### Public Key (Subject)

Every single actor _must_ have a subject, which is a 56-character public key made of up uppercase letters. These subject strings always start with the capital letter **M** (module). When actors are signed with embedded capability claims, they are always required to have a _subject_ claim. This means that invocation by public key is the **most reliable** means for locating target actors, though it can often impose some amount of friction on the part of the calling actors.

⚠️ **CAUTION** - You must be cautious when hard-coding public keys in your actors. If your software pipeline involves signing actors with _different private keys_ across environments, then that will change the public keys of those actors and result in error messages like "no such actor" being returned from attempted invocations.

One pattern to avoid this particular scenario is often called _self-linking_. This is where an actor is sent link configuration independent of a capability provider. This must be done through a `wash` script, direct access to the control interface, or via functions in the Rust `Host` API. Using this pattern lets operators pass the public keys of certain actors into an actor at runtime. There are some potential cons to this approach, most stemming from the fact that this introduces _state_ into what should otherwise be _stateless_ actors.

#### Call Alias

One potentially appealing compromise between the guaranteed uniqueness and reliability of public keys and the optional nature of OCI references is the use of a _call alias_.[^1] When actors are signed, they can optionally be signed with a `call_alias` claim. This alias is a single lowercase word or phrase separated by either underscores or dashes, e.g. `accounting` or `invest-accounting`.

When an actor with a call alias is started, the entire lattice in which that actor resides will be informed of the claim on this alias. If the alias is _already claimed_, then the newly started actor will _not_ be able to use it for identification purposes. This is the downside to using aliases: it is up to you to ensure that they are unique _per lattice_. They do not need to be globally unique.

Call aliases, when used appropriately, can provide consistent, developer-friendly naming conventions for locating target actors and often serve a similar purpose as the concept of "discovery servers" or "discovery services" in more traditional microservice environments.

### Using the Actor Core API

All actors in the wasmcloud ecosystem are capable of making calls to other actors. The wasmcloud host runtime allows for security plugins which can potentially _deny_ the invocation, but all compiled actors can access the RPC code. The function call for invoking an actor accepts a string, which can be any one of the 3 actor identification methods (wasmcloud can figure type which one by examining the string).

The following snippet of code illustrates the 3 different kinds of invocations for actor-to-actor calls (these OCI references and public keys are not real, and are used only for demonstration purposes):

{{% tabs %}}
   {{% tab "Rust" %}}
    let res =
      core::call_actor("wasmcloud.azurecr.io/testsample:0.1.0",
      data)?; // OCI reference
    let res2 =
      core::call_actor(
          "MASCXFM4R6X63UD5MSCDZYCJNPBVSIU6RKMXUPXRKAOSBQ6UY3VT3NPZ",
          data)?; // public key
    let res3 =
      core::call_actor("sample-test", data)?;
   {{% /tab %}}

   {{% tab "TinyGo" %}}
    res, err := actorcore.CallActor(
        "wasmcloud.azurecr.io/testsample:0.1.0",
        data);
    res, err := actorcore.CallActor(
        "MASCXFM4R6X63UD5MSCDZYCJNPBVSIU6RKMXUPXRKAOSBQ6UY3VT3NPZ",
        data);
    res, err := actorcore.CallActor("sample-test",
        data);
   {{% /tab %}}

   {{% tab "AssemblyScript" %}}
    actorcore.callActor(
        "wasmcloud.azurecr.io/testsample:0.1.0",
        data);
    actorcore.callActor(
        "MASCXFM4R6X63UD5MSCDZYCJNPBVSIU6RKMXUPXRKAOSBQ6UY3VT3NPZ",
        data);
    actorcore.callActor(
        "sample-test",
        data);
   {{% /tab %}}
 {{% /tabs %}}

### Communication Contract

In the preceding sample, you saw that a variable called `data` was used to pass a payload to the actor. The "call actor" API is opaque--it does not know or care about the contents of the payload you're sending. The only requirement is that the actor on the other side of the conversation know how to interpret what you've sent.

wasmcloud and waPC/widl already have conventions and tools for creating [actor interfaces](/app-dev/create-provider/cdd). While the example code for actor interfaces shows them being used by actors and capability providers, you can also have multiple actors share an actor interface. Since `widl` is already used for generating polyglot, waPC-compatible serialization code, it's ideal for creating actor-to-actor contracts.

[^1]: _Call aliases may not be introduced until version 0.15.1. Please check documentation before trying to use this feature_
