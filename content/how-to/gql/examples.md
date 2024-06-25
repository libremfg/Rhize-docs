---
title: 'Examples: GQL in manufacturing'
categories: ["concepts"]
description: Examples of useful GraphQL operations to use to automate and investigate your manufacturing processes
weight: 300
menu:
  main:
    parent: how-to-query
    identifier:
---

<!--- For each operation, add 1-2 sentences of context explaining when its useful.
Ground this in real manufacturing problems
--->


This document provides examples of useful GraphQL operations that you can copy and paste.
For detailed guides to using Rhize's GraphQL API, read [Call the GraphQL API]({{< relref "call-the-graphql-api" >}}) and [Query, mutate, subscribe]({{< relref "query-mutate-subscribe" >}}).


## Queries

These examples show useful queries to return information about your manufacturing process.
You can use them to build UIs or for investigation.

### Filter by multiple properties

If you want information about a specific subset of objects, use [filters]({{ relref "call-the-graphql-api#filter" }}) to define combinations.

This query uses filters and [cascade]({{ relref "call-the-graphql-api#cascade" }})
to return all material sub lots that have the properties `Thickness` and `Width`.

```graphql
query{
  queryMaterialSubLot @cascade{
      properties(filter:{label:{in:["Thickness","Width"]}},offset:1){
        label
      }
  }
}
```

## Mutations

These examples show how to change something in the database.
Usually, mutations either add information to your manufacturing model,
or initiate a workflow.

### Start a BPMN process

This snippet starts a BPMN process based on some user input.
You could use it to create a form or button in an interface for your plant:

```
```

