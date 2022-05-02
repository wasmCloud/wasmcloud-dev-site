---
title: "API Reference"
date: 2018-12-29T11:02:05+06:00
weight: 2
draft: false
description: "wadm API Specification"
---

The normal way to interact with a `wadm` installation (which could be a single server or a cluster) is through the [wash](../../wash) command-line tool. However, if you are planning on creating your own integration or writing a non-Rust language binding, then this reference will help.

### ⚠️ Caution
_wadm and its corresponding API are under active development_. Many components are not yet written at all. This document serves as a means to solicit feedback and collaborate on API design and will likely change multiple times.

A wadm cluster will use a single connection (and therefore set of credentials) for the API server. This allows a wadm cluster to use a different security context for the API server that it does for remotely controlling lattices.

## Topic Space
The wadm API is exposed entirely as a NATS service on a topic space. All of the API operations will occur as _requests_ on a topic in the following format:

```
wadm.api.{lattice-id}.{noun}.{verb}
```

All requests and responses on this topic are encoded as JSON.

If a consumer wishes to monitor events in real-time (for example, you want to be notified as soon as compensating actions are taken), then it can subscribe to the following topic:

```
wadm.evt.{lattice-id}
```

The events on this topic are JSON-encoded [CloudEvents](https://cloudevents.io/).

**NOTE** that all model _names_ are treated like unique identifiers and must conform to the rules governing NATS topic segments. For example, they cannot contain spaces, commas, unprintable characters, or periods.

## Model Persistence
The following operations pertain to storing and retrieving models. Persistence of models is explicitly and deliberately separated from deployment and deployment management.


### Get Model List
`wadm.api.{lattice}.model.list`

Retrieves a list of models within the given lattice. The status of the model is an aggregate of the statuses of the traits defined within. Only the newest version of the model is included in this list.

**Request**: `none`

**Response**: 
```json
{
    "name": "..",
    "version": "..",
    "description": "..",
    "deployed": true,
    "status": "compensating|ready|failed|undeployed"
}
```

### Get Model Spec
`wadm.api.{lattice}.model.info.{name}`

Retrieves the specification of the model stored with the given name. Caller must
specify the version of the model to be displayed. A list of stored versions can be obtained with the `versions` verb.

**Request**:
```json
{
    "version": "1.0"
}
```

**Response**:

JSON serialization of [OAM model/yaml](https://github.com/wasmCloud/wadm/tree/main/oam)



### Store Models
`wadm.api.{lattice}.model.put.{name}`

Stores the named model. If the model and version being submitted already exist, the request will be rejected. New versions will be appended to the history according to retention policy. Note that this won't automatically deploy a model, it only affects storage. The response will tell the caller how many versions are on file and the current version number _after_ the operation completed.

**Request**: JSON serialization of OAM model

**Response**:
```json
{
    "result": "error|created|newversion",
    "total_versions": 4,
    "current_version": "1.0",
    "message": "..."
}
```

The response will indicate if storage failed, if a new model was created, or if a new version of a model was pushed.


### Delete Models
`wadm.api.{lattice}.model.del.{name}`

Deletes the named model with the supplied version number. If the `delete_all` flag is set, then all versions of this model will be deleted. If the `undeploy` flag is true, then a corresponding `undeploy` operation will be performed on this model.

The response indicates success or failure, and if an `undeploy` operation was requested as part of the delete.

**Request**: 
```json
{
    "version": "1.0",
    "delete_all": false,
    "undeploy": false
}
```

**Response**:
```json
{
    "result": "deleted|error",
    "undeploy": true,
    "message": "..."
}
```

### Version History
`wadm.api.{lattice}.model.versions.{name}`

Retrieves a list of all stored versions of a given model, including when that version was created. 

**Request**: empty

**Response**: 
```json
[
    {
        "version": "...",
        "created": "<RFC3339 Timestamp>",
        "deployed": false
    }
]
```

Each of the items in the response list describes a revision and indicates whether that revision is the deployed one.

## Managing Deployments
Deployments are discrete instances of autonomous control agents that monitor an application model against the real-time, discovered state of a lattice. This agent monitors lattice events, compares lattice state against the desired state of the model, and issues lattice control interface commands in order to compensate for observed gaps.

### Deploy
`wadm.api.{lattice}.model.deploy.{name}`

Deploys the indicated model. This operation is idempotent, and will simply "succeed" if a request to deploy an already deployed model is received. The previous version, if deployed, does not need to be _undeployed_. Instead, the desired state of the new version is compared against the observed state of the lattice and the autonomous agents will compensate accordingly.

**Request**:
```json
{
    "version": "..version to deploy"
}
```

**Response**:
```json
{
    "result": "success|error",
    "message": "..."
}
```

It's important to note that the success/fail of this call doesn't indicate completed deployment, only success or failure of the receipt of the deployment request. In other words, the response is an _ack_.

### Undeploy
`wadm.api.{lattice}.model.undeploy.{name}`

Undeploys the currently deployed version of a model. If the model is already undeployed, the operation is idempotent and will return success along with an appropriate message. If the request field `destructive` is true, then the agent will attempt to remove all resources managed by the deployment from the lattice. Otherwise, previously managed resources will remain running. 

**Note** The response is delivered immediately as an acknowledgement, as it can take several minutes to complete a "destructive" undeploy.

**Request**: 
```json
{
    "destructive": false
}
```

**Response**:
```json
{
    "result": "success|error",
    "message": "..."
}
```

### Deployment Status
`wadm.api.{lattice}.model.status.{name}`

Describes the deployment status of a given model. Note that since only one version of a model is ever deployed at one time, we don't need to specify a version here. The currently deployed version is included in the response.

The response includes a `status` field which is an aggregate at high levels and an individual status for single traits.

**Request**: `none`

**Response**:
```json
{
    "version": "...",
    "status": 
        {
            "type": "undeployed|compensating|ready|failed",
            "message": "..."
        },
    "components": [
        {
            "name": "...",
            "type": "...",
            "status": {
                "type": "...",
                "message": "..."
            },
            "traits": [
                {
                    "name": "...",
                    "type": "spreadscaler|linkdef",
                    "status": {
                        "type": "...",
                        "message": "..."
                    }
                }
            ]
        }
    ]
}
```

### Deployment History
`wadm.api.{lattice}.model.history.{name}`

Returns the history of compensating actions taken by wadm. Also includes when deploy and undeploy actions were taken.

**Request**: `none`

**Response**:
```json
[
    {
        "time" : "<RFC3339 Timestamp>",
        "action": "modeldeploy|modelundeploy|compensator",
        "success": true,
        "message": "... varies ... ",
        "component": "varies",
        "trait": "varies",
        "details": {
            "key" : "value",
            "key2" : "value2"
        }
    }
]
```