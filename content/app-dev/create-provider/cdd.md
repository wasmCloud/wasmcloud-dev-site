---
title: "Contract-Driven Design"
date: 2018-12-29T11:02:05+06:00
weight: 6
draft: false
---

The first thing we're going to need for our _payments service_ sample capability provider is a **contract**. A contract describes the data types that actors and providers exchange, as well as the supported operations that can be invoked.

Contract-driven design and development has been a long-favored technique for developers building microservices and other types of composable systems. CDD not only makes our initial design experience easier, but it continues to pay dividends throughout the life cycle of our application.

### Creating a widl Schema

When designing our interface for the payments capability, we need to clearly define the list of operations and data types to be used. Before getting into the code used to do this, let's look at what we think the operation list might be:

* `AuthorizePayment` - Validates that a potential payment transaction can go through. If this succeeds then we should assume it is safe to complete a payment. Payments _cannot_ be completed without getting a validation code (in other words, all payments have to be pre-authorized).
* `CompletePayment` - Completes a previously authorized payment. This operation requires the "authorization code" from a successful authorization operation.
* `GetPaymentMethods` - Retrieves an _opaque_ list of payment methods, which is a list of customer-facing method names and the _[tokens](https://en.wikipedia.org/wiki/Tokenization_(data_security))_ belonging to that payment method. You could think of this list as a previously saved list of payment methods stored in a "wallet". A payment method _token_ is required to authorize and subsequently complete a payment transaction.

Now let's take a look at the payloads that might be used. Again, keep in mind that this is a use case example and a real implementation is likely to have far more detail.

#### AuthorizePaymentRequest

The list of fields on the `AuthorizePaymentRequest` message body:

| Field | Description |
| :--- | :--- |
| `amount` | Amount of the transaction, in cents. |
| `tax` | Amount of tax applied to this transaction, in cents. |
| `payment_method` | Token (string) of the payment method to be used |
| `payment_entity` | The entity (customer) requesting this payment |
| `reference_id` | Opaque Reference ID (e.g. order number) |

More complex implementations of a payment provider might require additional fields for currency and the exchange rate _at transaction time_.

#### AuthorizePaymentResponse

The list of fields returned by the payments provider in response to an authorization request:

| Field | Description |
| :--- | :--- |
| `success` | Indicates a successful authorization |
| `auth_code` | Optional string containing the tx ID of auth |
| `fail_reason` | Optional string w/rejection reason |

#### CompletePaymentRequest

In response to a successful authorization, a payment can be confirmed (real money withdrawn) using the following message body:

| Field | Description |
| :--- | :--- |
| `auth_code` | Authorization code from previous response |
| `request` | Original authorization request for validation |

#### CompletePaymentResponse

The payment capability will return the following message body after processing a payment request:

| Field | Description |
| :--- | :--- |
| `success` | Indicates if the tx was successful |
| `txid` | Receipt code / TX ID |
| `timestamp` | Timestamp (ms since epoch GMT) of TX |

#### PaymentMethodsList

In response to the empty query, a payments capabilty provider will return the following message body:

| Field | Description |
| :--- | :--- |
| `methods` | A map of `string->string` w/ tokens and descriptions |

### Generating the Actor Interfaces

Now that we have logically defined the contract to be shared by the payments-consuming actors and the payment capability provider, let's use some of the available wasmCloud tools to generate the actor interface code.

Let's take a look at some [waPC](/reference/wapc) **widl** to represent our contract:

```text
namespace "examples:payments"

interface {
  AuthorizePayment{request: AuthorizePaymentRequest}: 
      AuthorizePaymentResponse
  CompletePayment{request: CompletePaymentRequest}: 
      CompletePaymentResponse
  GetPaymentMethods(): PaymentMethodList  
}

type AuthorizePaymentRequest {
    amount: u32
    tax: u32
    payment_method: string
    payment_entity: string
}

type AuthorizePaymentResponse {
    success: bool
    auth_code: string?
    fail_reason: string?
}

type CompletePaymentRequest {
    auth_code: string
    request: AuthorizePaymentRequest
}

type CompletePaymentResponse {
    success: bool
    txid: string
    timestamp: u64
}

type PaymentMethodList {
    methods: {string:string}
}
```

At the moment, the code generation for **waPC** doesn't isolate the guest and host portions of the shared code the way we would like. Therefore, until the automated generation requires no hand-manipulation of the files, you can use existing actor interfaces as an example for how to apply minor tweaks. You can see the entire [payments-interface](https://github.com/wasmCloud/examples/tree/main/payments/payments-interface) Rust project to see what the code should look like.

Before proceeding to the next step, you should create your own `payments-interface` project by copying the one from the github repository. This project contains the schema that we've just created, a `Makefile` that generates code into a `src/generated.rs` file, and the appropriate dependencies in `Cargo.toml`.

If you want to learn more about using [waPC](/reference/wapc) to generate code from widl schemas, you can consult the reference guide.

Now that we have a Rust crate that we can use from both our actor and our capability provider, let's move on to the next step: _Creating a Capability Provider in Rust_.
