---
title: 'Agent configuration'
categories: ["reference"]
description: Authentication types for the Rhize agent
weight: 100
menu:
  main:
    parent: reference
    identifier:
---

The Rhize agent collects data that is emitted in the manufacturing process and makes this data visible in the Rhize system.
It works by connecting to equipment or groups of equipment that run over protocols such as OPC UA.

As the communication bridge between the Rhize Data Hub and your plant, the agent has multiple functions:
- It subscribes to tags and republishes the changes in NATS.
- It creates an interface for the BPMN engine to send reads and writes to a data source and its associated equipment.

## OPC UA authentication types

 When authenticating over OPC UA, Rhize supports the following authentication types:

| Authentication type | Behavior                                                                                                                                                                                       |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Anonymous           | Connects without any necessary credential                                                                                                                                                      |
| Username            | Authenticates through a `username` and `password` in the config file, or through a Kubernetes secret                                                                                                                           |
| Certificate         | Uses the certificate on disk specified in the  `OPCUA.CertFile` and `OPCUA.KeyFile` configs. If no certificate exists and the config specifies the `OPCUA.GenCert` property as `true`, automatically generates one. |
