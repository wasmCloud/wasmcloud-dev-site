---
title: "Creating a Provider Archive"
date: 2018-12-29T11:02:05+06:00
weight: 8
draft: false
---

A [provider archive](/reference/host-runtime/provider-archive) is a bundle containing a number of platform-specific (OS and CPU) plugin libraries (e.g. `.dll`, `.dylib`, `.so`) and an embedded, cryptographically signed JSON Web Token (JWT) that contains a set of claims attestations for the capability provider.

This provider archive is then something that can be uploaded to or downloaded from OCI registries, and passed in raw binary format to the wasmCloud `Host` API.

To build a capability provider, the first thing we'll need to do is make a release build. In this sample, we're building it on `Linux` (Ubuntu). From the root directory of the `fakepay-provider`, run:

```shell
cargo build --release
```

Once you've created a release build of the library, you can create a PAR (provider archive). This is done with the `wash par` command. You can find a robust example of this in the [Makefiles](https://github.com/wasmCloud/capability-providers/blob/main/http-server/Makefile) of our default scaffolding and example capability providers.

To show you how to do this without a makefile, run the following command (Linux). You will need a slightly different command depending on your CPU architecture and operating system:

```sh
wash par create \
  --name "Fake Payments" \
  --arch x86_64-linux \
  --binary target/release/libfakepay_provider.so \
  --capid examples:payments \
  --vendor wasmCloud \
  --version 1.0 \
  --revision 1 \
  --destination fakepay.par.gz \
  --compress
```

```sh
No keypair found in "/home/kevin/.wash/keys/kevin_account.nk".
We will generate one for you and place it there.
If you'd like to use alternative keys, you can supply them as a flag.

No keypair found in "/home/kevin/.wash/keys/libfakepay_provider_service.nk".
We will generate one for you and place it there.
If you'd like to use alternative keys, you can supply them as a flag.

Successfully created archive fakepay.par.gz
```

⚠️ **NOTE** that the `wash` command created a private issuer _seed key_ because I didn't have one. Additionally, it generated a _seed key_ for the provider archive itself and stored it in a file called `libfakepay_provider_service.nk`. The `wash` CLI will continue to re-use these keys for future signing, but when you move your provider to production you will want to pass explicit paths to the signing keys so that you can control the `issuer` and `subject` fields of the embedded token.

We can use `wash` to inspect a provider archive as well (primary key has been truncated to fit documentation):

```sh
wash par inspect fakepay.par.gz 
╔══════════════════════════════════════════════════════════════════════╗
║                        Fake Payments - Provider Archive              ║
╠═══════════════════════╦══════════════════════════════════════════════╣
║ Public Key            ║ VAN5FE2JCYMZFHZZWS2KNKVEIIU5...25XKFKCHXEMDU ║
╠═══════════════════════╬══════════════════════════════════════════════╣
║ Capability Contract ID║                            examples:payments ║
╠═══════════════════════╬══════════════════════════════════════════════╣
║ Vendor                ║                                    wasmCloud ║
╠═══════════════════════╬══════════════════════════════════════════════╣
║ Version               ║                                          1.0 ║
╠═══════════════════════╬══════════════════════════════════════════════╣
║ Revision              ║                                            1 ║
╠═══════════════════════╩══════════════════════════════════════════════╣
║                   Supported Architecture Targets                     ║
╠══════════════════════════════════════════════════════════════════════╣
║ x86_64-linux                                                         ║
╚══════════════════════════════════════════════════════════════════════╝
```

At this point we've decided on a logical contract for actors and capability providers to use for the _payments_ service. We created a (mostly) code-generated crate that can be declared as a dependency by both provider and actor, and we created a dummy implementation of the payments provider.

Next we'll write an actor that communicates with any payment provider, regardless of whether it's our fake implementation or not.