---
title: "Creating a new provider"
date: 2018-12-29T11:02:05+06:00
weight: 7
draft: false
---

Creating a capability provider involves creating a native executable. All capability provider executables have the same basic requirements:

- Accept a [Host Data](https://wasmcloud.github.io/interfaces/html/org_wasmcloud_core.html#host_data) structure from `stdin` immediately upon starting the executable. The host data is a base64 encoded JSON object with a trailing newline making it easy to pull from the `stdin` pipe.
- Accept linkdef messages according to the [RPC protocol](../../../reference/lattice-protocols/rpc)
- Communicate with actors via rpc messages defined by a capability contract
- Respond to periodic health checks

Thankfully, our scaffolding and reusable Rust crates take care of the basic plumbing for satisfying these requirements, letting you focus on messages exchanged with the actor, as defined by your capability contract.

### Create the New Project

Let's create a new provider project

```shell
wash new provider fakepay-provider
```

The command above creates a new capability provider project called `fakepay-provider`. Lets change to the newly created directory and start writing some code. The implementation below has all the methods a provider would need to implement to satisfy the capability contract `wasmcloud:example:payments`. You could paste this code into src/main.rs (replace the existing contents of that file). The functions themselves haven't been filled out - that's where your business logic goes. You can see that there is almost no boilerplate here, letting you stay focussed on your business logic.

```rust
//! Fakepay - stub payments capability provider
//!
use wasmbus_rpc::provider::prelude::*;
use wasmcloud_examples_payments::*;

// Start the provider and run until stopped by the host
fn main() -> Result<(), Box<dyn std::error::Error>> {
    provider_main(FakePayProvider::default())?;
    eprintln!("FakePay provider exiting");
    Ok(())
}

/// FakePay capability provider implementation
#[derive(Default, Clone, Provider)]
#[services(Payments)]
struct FakePayProvider {}

/// use default implementations of provider message handlers
impl ProviderDispatch for FakePayProvider {}
impl ProviderHandler for FakePayProvider {}

/// Handle FakePay methods
#[async_trait]
impl Payments for FakePayProvider {
    /// AuthorizePayment - Validates that a potential payment transaction
    /// can go through. If this succeeds then we should assume it is safe
    /// to complete a payment. Payments _cannot_ be completed without getting
    /// a validation code (in other words, all payments have to be pre-authorized).
    async fn authorize_payment(
        &self,
        _ctx: &Context,
        _arg: &AuthorizePaymentRequest,
    ) -> RpcResult<AuthorizePaymentResponse> {
        todo!()
    }

    /// Completes a previously authorized payment.
    /// This operation requires the "authorization code" from a successful
    /// authorization operation.
    async fn complete_payment(
        &self,
        _ctx: &Context,
        _arg: &CompletePaymentRequest,
    ) -> RpcResult<CompletePaymentResponse> {
        todo!()
    }

    /// `GetPaymentMethods` - Retrieves an _opaque_ list of payment methods,
    /// which is a list of customer-facing method names and the
    /// _[tokens](https://en.wikipedia.org/wiki/Tokenization_(data_security))_
    /// belonging to that payment method. You could think of this list as
    /// a previously saved list of payment methods stored in a "wallet".
    /// A payment method _token_ is required to authorize and subsequently
    /// complete a payment transaction. A customer could have previously
    /// supplied their credit card and user-friendly labels for those methods
    /// like "personal" and "work", etc.
    async fn get_payment_methods(&self, _ctx: &Context) -> RpcResult<PaymentMethods> {
        todo!()
    }
}
```

### Building the Provider

Before we can build it, you'll need to update Cargo.toml file to know about your project dependencies. Replace the contents of Cargo.toml with this:

```toml
[package]
name = "wasmcloud-example-provider-fakepay"
version = "0.1.0"
edition = "2018"
resolver = "2"

[dependencies]
async-trait = "0.1"
base64 = "0.13"
bytes = "1.0"
chrono = "0.4"
crossbeam="0.8"
futures = "0.3"
log = "0.4"
once_cell = "1.8"
rmp-serde = "0.15"
serde_bytes = "0.11"
serde_json = "1.0"
serde = {version = "1.0", features = ["derive"] }
thiserror = "1.0"
tokio = { version = "1", features = ["full"] }
toml = "0.5"
wasmcloud-examples-payments = { path="../../interface/payments/rust" }
wasmbus-rpc = "0.6"

[[bin]]
name = "fakepay"
path = "src/main.rs"
```

Change the path of the interface file to match your location.

Also, we need to make one edit to the project Makefile: change the last part of CAPABILITY_ID to replace "fakepay_provider" with "payments", to match the capability contract we defined in the interface:

```Makefile
CAPABILITY_ID = "wasmcloud:examples:payments"
```

Let's [create the provider archive](./create-par/)
