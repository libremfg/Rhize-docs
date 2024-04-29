---
title: 'NATS configuration'
date: '2023-10-04T10:22:15-03:00'
categories: ["reference"]
description: Values and parameters to configure NATS in your Rhize operation
weight: 200
menu:
  main:
    parent: reference
    identifier: nats-reference
---

Rhize uses the [NATS message broker](https://nats.io/) for its publish-subscribe messaging.
Through NATS, Rhize can decouple services, exchange messages in real-time,
and receive event data from all levels of the operation.

These sections describe NATS parameters that are particularly relevant to Rhize's configuration.
For general use, refer to the [NATS official documentation](https://docs.nats.io/nats-concepts/overview).

## Reserved topics

Topics that begin with a dollar sign ($) denote topics specifically about the NATS system.

### Jet stream (`$JS`)

The `$JS` topic is reserved for messages about the NATS [JetStream](https://docs.nats.io/nats-concepts/jetstream).

### Key value store (`$KV`)

The `$KV` topic is reserved for messages about the [Key/Value Store](https://docs.nats.io/nats-concepts/jetstream/key-value-store).

Subtopics include the following:

- `$KV/JobResponses`

## BPMN topics and configuration

The `libreBPMN` topic is for messages about the BPMN engine.
Subtopics include the following:

- `libreBPMN/command/START_EVENT`
- `libreBPMN/command/TASK_COMPLETE`
- `libreBPMN/command/SERVICE_TASK`
- `libreBPMN/command/EXCLUSIVE_GATEWAY`

### Streams

- `libreBpmn_Command`
- `LibreTimerStart`
- `JobResponses KV`
- `WorkflowSpecificationKV`

## NATS configuration

The following parameters configure the NATS message queues for different services.

### BPMN

The NATS configuration parameters for the BMPN streams are as follows:

- `CommandStreamReplicas`
- `JobResponseKVMaxGB`
- `JobResponseKVReplicas`
- `JobResponseKVTTLMinutes`
- `WorkflowSpecificationKVReplicas`

For example:

```json
 "NATS": {
    "CommandStreamReplicas": 1,
    "JobResponseKVMaxGB": 2,
    "JobResponseKVReplicas": 1,
    "JobResponseKVTTLMinutes": 7,
    "WorkflowSpecificationKVReplicas": 1
  },
```

### Libre core

The NATS configuration parameters for the Libre core topics are as follows:
- `serverUrl`
- `replicas`
