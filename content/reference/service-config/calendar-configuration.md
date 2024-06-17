---
title: 'Calendar Service configuration'
categories: ["reference"]
description: Configuration for the Rhize Calendar Service
weight: 900
menu:
  main:
    parent: reference-service-config
    identifier:
---

 The Calendar Service handles polling work calendar definitions and generating work calendar entries in the graph and time series databases.

## `logging`

 Logs the configuration to the console.

| Attributes          | Description                                                                                                                                                                                    |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `type`              | Specifies the logging configuration type: `json`, `multi`, or console. <br />(Default: `console`)                                                                                   |                                                                                                                                                 
| `Level`             | Configures the level of logging: `Trace`, `Debug`, `Info`, `Warn`, `Error`, `Fatal`, and `Panic`. <br />(Default: `Trace`)                                                                     |

## `NATS`

 Message broker that drives Rhize's event-based architecture.

| Attributes          | Description                                                                                                                                                                                    |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `SERVER_URL`         | The URL of the NATS server. <br />(Default: `nats://system:system@localhost:4222`)                                                              |


## `OIDC`

 Configurations for Keycloak authentication and connection with OpenID Connect.

| Attributes          | Description                                                                                                                                                                                    |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `serverUrl`         | The URL of the OpenID Connect server. <br />(Default: `http://localhost:8090`)    |                                                                                                                    
| `realm`             | Identifies the authentication domain for which the authentication request is being made.                                                                              |
| `client_id`         | The unique identifier assigned to the client application by the OIDC server.                                                                                      |
| `client_secret`     | Used to authenticate the client when making confidential requests.                                                                       |
| `username`          | The username credentials of the user who is attempting to authenticate with the OIDC server.                                                           |
| `password`          | The password credentials of the user who is attempting to authenticate with the OIDC server.                                                                         |

## `OpenTelemetry`

| Attributes          | Description                                                                                                                                                                                    |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `serverUrl`         | The URL of the OpenTelemetry server. <br />(Default: `localhost:4317`)                                                                                                                         | 
| `samplingRate`      | The sampling rate for traces. <br />(Default: `1`)                                   |

## `libreDataStoreGraphQL`

| Attribute | Description |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GRAPHQL_URL`         | The URL of the GraphQL endpoint to use for interacting with Rhize services. <br />(Default: `http://localhost:8080/graphql`) |

## `GraphQLSubscriber`

| Attribute | Description |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GRAPHQL_URL`         | The URL of the GraphQL endpoint for the GraphQLSubscriber. <br />(Default: `http://localhost:4000/`)|

## `RESTAPI`

| Attribute | Description |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `PORT`         | The port number for RestAPI connection.  <br />(Default: `8080`)                                                                                                                                    |

## `Calendar`

Specific configuration options for the calendar service.

| Attribute | Description |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `QueryIntervalMinutes`         | How often to poll the work calendar definitions <br />(Default: `10`)|

## `QUERY`

Query options specific to the calendar service

| Attribute | Description |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `hierarchyScopeRecursionDepth`         | How deep to recurse through the hierarchy scope hierarchy <br />(Default: `3`)|
| `equipmentRecursionDepth`         | How deep to recurse through the equipment hierarchy <br />(Default: `3`)|

## `Influx3`

InfluxDB3 server options

| Attribute | Description |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Database`         | The name of the database to connect to <br />(Default: `Libre_calendar-service`)|
| `Host`         | The host of the Influx3 database <br />(Default: `http://localhost:8096`)|
| `Organization`         | The Influx3 Organization (Influx3 Cloud) <br />(Default: `Libre`)|
| `TokenPrefix`         | The prefix used in the authorization token (`Token` for Influx 3 cloud, `Bearer` for Influx3 Edge) <br />(Default: `Token`)|
| `Token`         | The authorization token to attach to any Influx3 requests|

## `Postgres`

Postgres server options

| Attribute | Description |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Host`         | The hostname of the Postgres database <br />(Default: `localhost`)|
| `Port`         | Those port of the Postgres database <br />(Default: `5432`)|
| `User`         | The Postgres user name <br />(Default: `postgres`)|
| `Password`         | The postgres instance password <br />(Default: `postgres`)|
| `Database`         | The database name for the postgres instance <br />(Default: `Libre`)|

## `Database`

Which database instance to use, either `Postgres` or `Influx3` <br />(Default: `Influx3`)
