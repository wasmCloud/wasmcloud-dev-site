---
title: "Creating a Rust Provider"
date: 2018-12-29T11:02:05+06:00
weight: 7
draft: false
---

Creating a Rust provider also involves creating a new Rust library. In this library, we need to implement the `CapabilityProvider` trait and declare some basic dependencies required by all capability providers. We're also going to want to declare a dependency on the actor interface we created in the previous step.

While we can use scaffolding, we'll go through it manually so you can see everything that needs to happen. First, create a new Rust crate:

```shell
cargo new --lib fakepay-provider
```

We're going to create a _fake_ implementation of the payments capability provider. For obvious reasons, it will be far easier for us to play around and test if we're not trying to make real credit or bank transactions. Here's what our `Cargo.toml` for this new project should look like (the versions may be slightly newer than what you see here):

```toml
[package]
name = "fakepay-provider"
version = "0.1.0"
authors = ["wasmcloud Team"]
edition = "2018"


[features]
static_plugin = [] # Enable to statically compile this into a host

[lib]
crate-type = ["cdylib", "rlib"]


[dependencies]
chrono = "0.4.19"
env_logger = "0.8.2"
log = "0.4.14"
uuid = { version = "0.8.2", features = ["serde", "v4"] }
wascc-codec = "0.9.0"
payments-interface = { path = "../payments-interface" }
actor-core = { git = "https://github.com/wasmcloud/actor-interfaces", branch = "main" }
```

There are a couple of things we've put in here because we know we're going to need them soon and, by convention, we like to build capability providers that can either be loaded by virtue of [provider archive](/reference/host-runtime/provider-archive) files or as a direct dependency in a Rust crate.

Now we'll provide an implementation of the `CapabilityProvider` interface (a trait we get from **wasmcloud-codec**) that exposes our fake payment capability. Before we start coding any real functionality, our stubbed out `lib.rs` file in the new provider looks like this:

```rust
#[macro_use]
extern crate wascc_codec as codec;
#[macro_use]
extern crate log;

use std::sync::{Arc, RwLock};

use actor_core::{deserialize, serialize, CapabilityConfiguration, HealthCheckResponse};
use codec::capabilities::{CapabilityProvider, Dispatcher, NullDispatcher};
use codec::core::{OP_BIND_ACTOR, OP_HEALTH_REQUEST, OP_REMOVE_ACTOR};

/// The well-known contract ID for this provider
pub const CAPABILITY_ID: &str = "examples:payments";

#[cfg(not(feature = "static_plugin"))]
capability_provider!(FakePaymentsProvider, FakePaymentsProvider::new);

/// An example implementation of the `examples:payments` contract.
#[derive(Clone)]
pub struct FakePaymentsProvider {
    dispatcher: Arc<RwLock<Box<dyn Dispatcher>>>,
}

impl CapabilityProvider for FakePaymentsProvider {
    fn configure_dispatch(
        &self,
        dispatcher: Box<dyn Dispatcher>,
    ) -> Result<(), Box<dyn std::error::Error + Send + Sync>> {
        todo!()
    }

    fn handle_call(
        &self,
        actor: &str,
        op: &str,
        msg: &[u8],
    ) -> Result<Vec<u8>, Box<dyn std::error::Error + Send + Sync>> {
        todo!()
    }

    fn stop(&self) {
        todo!()
    }
}
```

Since we don't have any meaningful resources to dispose of when the host stops, we can simply provide a blank implementation for `stop`. A capability provider will be given _one_ dispatcher instance upon startup. This function will never be called twice and is used to give the capability provider an instance of something that implements the `Dispatcher` trait, which defines how providers can invoke actors. We also want to provide a `new` function for the `FakePaymentsProvider`:

```rust
/// An example implementation of the `examples:payments` contract.
#[derive(Clone)]
pub struct FakePaymentsProvider {
    dispatcher: Arc<RwLock<Box<dyn Dispatcher>>>,
}

impl FakePaymentsProvider {
    pub fn new() -> Self {
       let _ = env_logger::try_init();            
       FakePaymentsProvider {
            dispatcher: 
              Arc::new(RwLock::new(Box::new(NullDispatcher::new()))),
        }
    }
}

impl CapabilityProvider for FakePaymentsProvider {
    fn configure_dispatch(
        &self,
        dispatcher: Box<dyn Dispatcher>,
    ) -> Result<(), Box<dyn std::error::Error + Send + Sync>> {
        info!("Dispatcher configured.");

        let mut lock = self.dispatcher.write().unwrap();
        *lock = dispatcher;

        Ok(())
    }

    fn handle_call(
        &self,
        actor: &str,
        op: &str,
        msg: &[u8],
    ) -> Result<Vec<u8>, Box<dyn std::error::Error + Send + Sync>> {
        todo!()
    }

    fn stop(&self) {
        // Do nothing
    }
}
```

If you're curious about why we're using `Arc<RwLock<T>>` for holding the dispatcher, it's because we need interior mutability (the `CapabilityProvider` trait does not have mutable self references) and if this was an asynchronous provider, we'd need thread-safe access to the dispatcher at some point.

Now we're ready to provide some handlers for both the mandatory **wasmcloud** operations and the operations that are part of our shared `examples:payments` contract. Let's update the code so that our `handle_call` function looks as follows:

```rust
fn handle_call(
        &self,
        actor: &str,
        op: &str,
        msg: &[u8],
    ) -> Result<Vec<u8>, Box<dyn std::error::Error + Send + Sync>> {
        trace!("Handling operation `{}` from `{}`", op, actor);

        match op {
            OP_BIND_ACTOR if actor == "system" => {
                // Provision per-actor resources here
                Ok(vec![])
            }
            OP_REMOVE_ACTOR if actor == "system" => {
                let cfgvals = 
                  deserialize::<CapabilityConfiguration>(msg)?;
                info!("Removing actor configuration for {}", 
                  cfgvals.module);
                // Clean up per-actor resources here
                Ok(vec![])
            }
            OP_HEALTH_REQUEST if actor == "system" => 
              Ok(serialize(HealthCheckResponse {
                healthy: true,
                message: "".to_string(),
            })?),

            // contract-specific handlers
            OP_AUTHORIZE_PAYMENT => 
              self.authorize_payment(deserialize(&msg)?),
            OP_COMPLETE_PAYMENT => 
              self.complete_payment(deserialize(&msg)?),
            OP_GET_PAYMENT_METHODS => 
              self.get_payment_methods(),
            _ => Err(format!("Unsupported operation `{}`", op).into()),
        }
    }
```

We can add our fake implementations to the `FakePaymentsProvider` implementation:

```rust
impl FakePaymentsProvider {
    pub fn new() -> Self {
        let _ = env_logger::try_init();
        FakePaymentsProvider {
            dispatcher: 
              Arc::new(RwLock::new(Box::new(NullDispatcher::new()))),
        }
    }

    fn authorize_payment(
        &self,
        _req: AuthorizePaymentRequest,
    ) -> Result<Vec<u8>, Box<dyn Error + Send + Sync>> {
        Ok(serialize(&AuthorizePaymentResponse {
            success: true,
            auth_code: Some(uuid::Uuid::new_v4().to_string()),
            fail_reason: None,
        })?)
    }

    fn complete_payment(
        &self,
        _req: CompletePaymentRequest,
    ) -> Result<Vec<u8>, Box<dyn Error + Send + Sync>> {
        Ok(serialize(&CompletePaymentResponse {
            success: true,
            txid: uuid::Uuid::new_v4().to_string(),
            timestamp: chrono::Utc::now().timestamp_millis() as u64,
        })?)
    }

    fn get_payment_methods(&self) -> 
      Result<Vec<u8>, Box<dyn Error + Send + Sync>> {
        Ok(serialize(&PaymentMethodList {
            methods: fake_methods(),
        })?)
    }
}
```

This implementation automatically approves all authorization requests and lets all payment completion requests go through. If you want to get fancy you can add environment variables that control the failure rate of authorizations, etc.

Lastly, we just need to provide an implementation for the `fake_methods` function that provides a list of payment method tokens and their customer-facing names:

```rust
fn fake_methods() -> HashMap<String, String> {
    let mut hm = HashMap::new();
    hm.insert("token1".to_string(), "PayBuddy".to_string());
    hm.insert("token2".to_string(), "Bank of Exemplar".to_string());

    hm
}
```

You can find the full code for the fake payment service capability provider [here](https://github.com/wasmcloud/examples/tree/master/ecommerce/fakepay-provider).

Next, we'll create a new private key for this provider and create and sign a _provider archive_ file.
