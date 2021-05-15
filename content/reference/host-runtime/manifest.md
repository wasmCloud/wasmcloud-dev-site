---
title: "Host Manifests"
date: 2018-12-29T11:02:05+06:00
weight: 9
draft: false
---

A _host manifest_ is a representation of a _desired_ set of contents for a running `wasmcloud` host instance, including actors, capability providers, host labels, and link definitions. The host manifest can either be a `json` file or a `yaml` file, provided they have the following sections:

* `labels` - A set of arbitrary key-value pairs that are associated with the host. Host labels can be queried as part of lattice interrogations and used by the _auction_ process of the wasmCloud control interface to determine scheduling suitability for actors and providers. This section is optional.
* `actors` - This section contains a list (yaml or json `array`) of actor _references_. An actor reference in a host manifest can be either the actor's public key _or_ the OCI image reference URL of the actor as stored in an OCI registry _or_ a path to a `.wasm` file. This section is mandatory, so if you do not wish to start any actors, supply the format equivalent of an empty list/array.
* `capabilities` - This section contains a list of capability _descriptions_. A capability description is a structure that provides the minimal amount of information required for the wasmCloud host to load and start a capability provider. Capability descriptions contain the following fields:
  * `image_ref` - This is a required field that contains the OCI image reference of a capability provider. If you want to launch a local capability provider via manifest, then you'll have to ensure that it's stored in a local registry and you provide the local registry URL.
  * `link_name` - This field is optional and contains the name of the provider as a target for links. If you leave this value out of the manifest, wasmCloud will use the default link name of `default`.  This field is only necessary to distinguish between two configurations of the same provider.  One may, for example, need to run two configurations of a `keyvalue` provider.
    In this case there may be two entries in the `capabilities` array with the same `image_ref`, but different values for `link_name`.  When there is only one configuration of any given provider in the manifest, this field should be omitted.
* `links` - This required field contains a list of "link definitions". If you do not have any link definitions, you will have to supply the format equivalent of an empty list or array (e.g. `[]`). A link definition contains the following fields:
  * `actor` - The public key of the actor in this link. Note that even if you start an actor via an OCI reference, you must _only_ ever use the actor's public key in a link definition.
  * `contract_id` - The contract ID of the link. This corresponds to the contract ID supported by the specific capability provider being linked, e.g. `wasmcloud:httpserver` or `wasmcloud:keyvalue`, etc.
  * `link_name` - Optional field, specify this value if you are referring to a provider that used an alternate link name when being started.
  * `values` - An optional map containing key-value pairs to be supplied as the per-actor configuration sent to the capability provider at link time.

A host manifest file can be supplied as a path to the `wasmcloud` binary at startup, or you can apply manifests in your own host-embedded application by using the `apply_manifest` wasmcloud-host API function call.

## Environment Variable Substitution

Within the YAML manifest files, you can use environment variable substitution syntax that lets you supply a default value that will be overridden when a named environment variable has been provided. That syntax looks as follows:

```shell
${BROKERCHANNEL_ACTOR:MAP7EXS72YJ5Z6U5AJ6IMYIRVMRJRZJBLVTC6H4E552K7SJDRYBPA3YF}
```

Here the environment variable `BROKERCHANNEL_ACTOR` will be used, otherwise the public key `MAP....F` will be used.

## Example Manifest (YAML)

The following is an example manifest that describes a subset of functionality contained in the "wasmCloud chat" reference/demo application.

```yaml
---
labels:
    sample: "wasmCloud Chat"
actors:
    - ../actors/messages/target/wasm32-unknown-unknown/debug/messages_s.wasm
    - ../actors/broker-channel/target/wasm32-unknown-unknown/debug/broker_channel_s.wasm
capabilities:
    - image_ref: wasmcloud.azurecr.io/nats:0.10.3
      link_name: frontend
    - image_ref: wasmcloud.azurecr.io/nats:0.10.3
      link_name: backend
    - image_ref: wasmcloud.azurecr.io/logging:0.9.3
    - image_ref: wasmcloud.azurecr.io/redisstreams:0.5.1
links:
  # configure the broker channel actor - messaging, logging, extras
  - actor: ${BROKERCHANNEL_ACTOR:MAP7EXS72YJ5Z6U5AJ6IMYIRVMRJRZJBLVTC6H4E552K7SJDRYBPA3YF}
    contract_id: "wasmcloud:messaging"
    provider_id: "VADNMSIML2XGO2X4TPIONTIC55R2UUQGPPDZPAVSC2QD7E76CR77SPW7"
    link_name: frontend
    values:
      SUBSCRIPTION: wcc.frontend.requests,wcc.backend.events.>
  - actor: ${BROKERCHANNEL_ACTOR:MAP7EXS72YJ5Z6U5AJ6IMYIRVMRJRZJBLVTC6H4E552K7SJDRYBPA3YF}
    contract_id: "wasmcloud:logging"
    provider_id: "VCCANMDC7KONJK435W6T7JFEEL7S3ZG6GUZMZ3FHTBZ32OZHJQR5MJWZ"
  - actor: ${BROKERCHANNEL_ACTOR:MAP7EXS72YJ5Z6U5AJ6IMYIRVMRJRZJBLVTC6H4E552K7SJDRYBPA3YF}
    contract_id: "wasmcloud:extras"
    provider_id: "VDHPKGFKDI34Y4RN4PWWZHRYZ6373HYRSNNEM4UTDLLOGO5B37TSVREP"
  
  # the messages actor needs: eventstreams, messaging, logging
  - actor: ${MESSAGES_ACTOR:MD6EZIZRZ5VCN6FSJZXHE2NBGGWSIIKP2GDDTODFHOTGNIVIDF2MPHIK}
    contract_id: "wasmcloud:messaging"
    provider_id: "VADNMSIML2XGO2X4TPIONTIC55R2UUQGPPDZPAVSC2QD7E76CR77SPW7"
    link_name: backend
    values: {}  # no subscriptions on the backend for this actor, only publishes
  - actor: ${MESSAGES_ACTOR:MD6EZIZRZ5VCN6FSJZXHE2NBGGWSIIKP2GDDTODFHOTGNIVIDF2MPHIK}
    contract_id: "wasmcloud:logging"
    provider_id: "VCCANMDC7KONJK435W6T7JFEEL7S3ZG6GUZMZ3FHTBZ32OZHJQR5MJWZ"
  - actor: ${MESSAGES_ACTOR:MD6EZIZRZ5VCN6FSJZXHE2NBGGWSIIKP2GDDTODFHOTGNIVIDF2MPHIK}
    contract_id: "wasmcloud:eventstreams"
    provider_id: "VC5EWH6XQOSV7RHTD7HOHBNZURBIRW7GK5DH3PJNV2OCAUOGXDTC4UCN"    
    values:
      URL: redis://0.0.0.0:6379/ 
```

## Caveats

There are a few things to keep in mind while using manifest files:

* Whenever using relative paths, keep in mind that the path is relative to the `wasmcloud` binary at execution time, and _not_ relative to the location of the YAML file.
* When you specify link definitions, those definitions exist _lattice-wide_, and could therefore affect actors and capability providers not even defined within your local manifest file. You'll need to be dilligent about scope awareness when applying manifests to a fully networked lattice.
