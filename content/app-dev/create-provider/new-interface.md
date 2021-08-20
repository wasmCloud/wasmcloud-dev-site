---
title: "Creating an Interface"
date: 2018-12-29T11:02:05+06:00
weight: 6
draft: false
---

The first thing we're going to need for our _payments service_ sample capability provider is an **interface**. An interface describes the data types that actors and providers exchange, as well as the supported operations that can be invoked, and the relative directions of those operations.

[Contract-driven](https://en.wikipedia.org/wiki/Design_by_contract) design and development has been a long-favored technique for developers building microservices and other types of composable systems. CDD not only makes our initial design experience easier, but it continues to pay dividends throughout the life cycle of our application.

### Creating a Smithy Interface

When designing our interface for the payments capability, we need to clearly define the list of operations and data types to be used. Before getting into the code used to do this, let's look at what we think the operation list might be:

* `AuthorizePayment` - Validates that a potential payment transaction can go through. If this succeeds then we should assume it is safe to complete a payment. Payments _cannot_ be completed without getting a validation code (in other words, all payments have to be pre-authorized).
* `CompletePayment` - Completes a previously authorized payment. This operation requires the "authorization code" from a successful authorization operation.
* `GetPaymentMethods` - Retrieves an _opaque_ list of payment methods, which is a list of customer-facing method names and the _[tokens](https://en.wikipedia.org/wiki/Tokenization_(data_security))_ belonging to that payment method. You could think of this list as a previously saved list of payment methods stored in a "wallet". A payment method _token_ is required to authorize and subsequently complete a payment transaction. A customer could have previously supplied their credit card and user-friendly labels for those methods like "personal" and "work", etc.

Now let's take a look at the payloads that might be used. Again, keep in mind that this is a use case example and a real implementation is likely to have far more detail.

#### AuthorizePaymentRequest

The list of fields on the `AuthorizePaymentRequest` message body:

| Field | Description |
| :--- | :--- |
| `amount` | Amount of the transaction, in cents |
| `tax` | Amount of tax applied to this transaction, in cents |
| `paymentMethod` | Token (string) of the payment method to be used |
| `paymentEntity` | The entity (customer) requesting this payment |
| `referenceId` | Opaque Reference ID (e.g. order number) |

More complex implementations of a payment provider might require additional fields for currency and the exchange rate _at transaction time_.

#### AuthorizePaymentResponse

The list of fields returned by the payments provider in response to an authorization request:

| Field | Description |
| :--- | :--- |
| `success` | Indicates a successful authorization |
| `authCode` | Optional string containing the tx ID of auth |
| `failReason` | Optional string w/rejection reason |

#### CompletePaymentRequest

In response to a successful authorization, a payment can be confirmed (real money withdrawn) using the following message body:

| Field | Description |
| :--- | :--- |
| `authCode` | Authorization code from previous response |
| `request` | Original authorization request for validation |

#### CompletePaymentResponse

The payment capability will return the following message body after processing a payment request:

| Field | Description |
| :--- | :--- |
| `success` | Indicates if the tx was successful |
| `txid` | Receipt code / TX ID |
| `timestamp` | Timestamp (ms since epoch GMT) of TX |

#### PaymentMethodsList

In response to the empty query, a payments capability provider will return the following message body:

| Field | Description |
| :--- | :--- |
| `methods` | A map of `string->string` w/ tokens and descriptions |

### Generating the Actor Interfaces

Now that we have logically defined the contract to be shared by the payments-consuming actors and the payment capability provider, let's use some of the available wasmCloud tools to generate the actor interface code.

Let's take a look at some [smithy](https://awslabs.github.io/smithy/) IDL to represent our contract:

```text
TODO
```

With this interface definition in hand, let's create a new interface project:

```shell
wash new interface payments-interface ./path/to/interface.smithy
```

This creates a new Rust project called `payments-interface` and generates code based on the `.smithy` file you specify as a parameter. Since our scaffolding comes ready to build, you can simply type `make` at the root of the project and you've now got a library that can be reused by both actors and capability providers for seamless communication.

Now that we have a Rust crate that we can use from both our actor and our capability provider, let's move on to the next step: _Creating a Capability Provider in Rust_.
