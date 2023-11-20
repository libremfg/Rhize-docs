---
title: 'Create objects from the UI'
date: '2023-11-20T15:36:03-03:00'
draft: false
categories: ["how-to"]
description: How to Ui
weight: 010
menu:
  main:
    parent: how-to-define
    identifier: how-to-workmaster-ui
---

To make a production object visible to the Rhize data hub, you must define it as a data model.
Along with is API, Rhize also has a graphical interface to create and update objects in your role-based equipment hierarchy.

Often, one object references another: for example, a piece of equipment may belong to an equipment class, have a unit of measure as a property, and be associated with process segment.
These associations form nodes and edges in your knowledge graph, so the more information relationships that you accurately create, the better.

## Procedure

1. From the UI, select the menu in the top corner.
1. Select **Master Data**, then the object you want to configure.
1. **Create new** (or the plus sign).
1. Name the object according to your naming conventions.
1. Fill in the other fields. For details, refer to the [Master definitions and fields]({{< relref "master-definitions" >}}).

You can create many different objects, all with their own parameters and associations.
For that reason, a general procedure such as the preceding lacks any interesting detail.

To make the action more concrete,
the next section provides an example to create plausible group of objects.

## Example: create an oven class with an instance

Consider a company that makes brownies.
Within one of its sites, this firm has a work center called `brownie_kitchen_1`.
This kitchen has `oven_123`, an instance of the `Oven` class.
This equipment item also has a data source that gives temperature readings, which are published to a dashboard.

Here's how you could use the Rhize UI to model this.

{{< notice "note" >}} 
If you are actively following to learn, make sure to use names that will easily identify the objects as example objects for testing.
{{< /notice >}}

Model the `Oven` equipment class:
1. From **Master Data**, select **Equipment Class**.
1. Give it a name that makes sense for your organization. Give it a description, such as `Oven for baking`.
1. Add any additional properties.
1. **Create** .
1. Make it active by changing its version state.

Create the associated data source:
1. From **Master Data**, select **Data Source**.
1. Add the source's connection string and protocol, along with any credentials (to configure authentication, refer to [Agent configuration]({{< ref "agent-configuration" >}}).
1. Select the **Topics** tab and add the label and data type.
1. **Create** and make the version active.

Now, create an instance of the Oven.

1. From **Master Data**, select **Equipment.**
1. Add its unique, globally identifiable ID and give it a description.
1. For **Equipment Class**, add the `Oven` class you just created.
1. For **Equipment Level**, select `WorkCenter`.
1. **Create.** 

   After the object is successfully created, you can add the data source.
1. From the **Data Sources** tab, select **Link Data Sources**. Select the data source you just created.

On success, your UI should now have an item equipment that is associated with an equipment level, equipment class, and data source.
For a complete reference of all objects and properties that you can add through the UI, refer to the [Master definitions and Fields]({{< relref "master-definitions" >}}).

