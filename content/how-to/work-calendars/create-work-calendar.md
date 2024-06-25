---
title: Create work calendars
description: >-
  A guide to creating work calendars.
weight: 200
menu:
  main:
    parent: howto-work-calendars
---

This guide shows you how to create a work calendar using the Rhize GraphQL API.
As a calendar has associations with multiple other entities,
the process involves a series of [mutations]({{< relref "how-to/gql/mutate" >}}) to create
associated data.

To learn how work calendars work,
read [About work calendars]({{< ref "about-calendars-and-overrides" >}}).

## Prerequisites


To use the work calendar service, ensure you have the following:
- The [calendar service installed]({{< relref "/deploy/install/services" >}})
- A plan for how to organize and name your calendars according to equipment.

## Procedure

In short, the procedure works as follows:

1. Add equipment that follows some hierarchy.
1. Add hierarchy scopes for the calendar rules. These scopes should map to the equipment hierarchy.
1. Add work calendar definitions.

You can add these objects in the UI or through the GraphQL API.
The following sections provide the requirements for each of these entities and examples of a mutation to create them.


{{< bigFigure
src="/images/work-calendars/diagram-rhize-work-calendar-relationships.png"
caption="The calendar service uses the relationships between equipment, hierarchy scope, and work calendars."
alt="Diagram of relationship between three configurations"
width="50%"
>}}
  
### Add equipment
  
The first step is to add an equipment hierarchy.

**Requirements.**
- For an equipment calendar state to be recorded, it must have an active version. 

**Example**

This mutation adds multiple items of equipment in a batch.
Note that some items, such as `Equipment A`, `Equipment B`, and `Equipment C`,
have links to child equipment, as expressed in the `isMadeUpOf` property.
These relationships express the equipment hierarchy.

{{< expandable title="mutation addEquipment" >}}
```gql
mutation AddEquipment($input: [AddEquipmentInput!]!, $upsert: Boolean) {
  addEquipment(input: $input, upsert: $upsert) {
    numUids
  }
}
{
  "input": [
    {
      "id": "Equipment A",
      "nextVersion": "2",
      "label": "Equipment A",
      "activeVersion": {
        "id": "Equipment A",
        "version": "1",
        "versionStatus": "ACTIVE",
        "equipment": {
          "id": "Equipment A"
        }
      },
      "isMadeUpOf": [
        {"id": "Equipment B"},
        {"id": "Equipment D"}
      ]
    },
    {
      "id": "Equipment B",
      "nextVersion": "2",
      "label": "Equipment B",
      "activeVersion": {
        "id": "Equipment B",
        "version": "1",
        "versionStatus": "ACTIVE",
        "equipment": {
          "id": "Equipment B"
        }
      },
      "isMadeUpOf": [
        {"id": "Equipment C"}
       
      ]
    },
    {
      "id": "Equipment C",
      "nextVersion": "2",
      "label": "Equipment C",
      "activeVersion": {
        "id": "Equipment C",
        "version": "1",
        "versionStatus": "ACTIVE",
        "equipment": {
          "id": "Equipment C"
        }
      },
      "isMadeUpOf": [
        {"id": "Equipment Ca"},
        {"id": "Equipment Cb"}
      ]
    },
    {
      "id": "Equipment Ca",
      "nextVersion": "2",
      "label": "Equipment Ca",
      "activeVersion": {
        "id": "Equipment Ca",
        "version": "1",
        "versionStatus": "ACTIVE",
        "equipment": {
          "id": "Equipment Ca"
        }
      }
    },
    {
      "id": "Equipment Cb",
      "nextVersion": "2",
      "label": "Equipment Cb",
      "activeVersion": {
        "id": "Equipment Cb",
        "version": "1",
        "versionStatus": "ACTIVE",
        "equipment": {
          "id": "Equipment Cb"
        }
      }
    },
    {
      "id": "Equipment D",
      "nextVersion": "2",
      "label": "Equipment D",
      "activeVersion": {
        "id": "Equipment D",
        "version": "1",
        "versionStatus": "ACTIVE",
        "equipment": {
          "id": "Equipment D"
        }
      }
    }
  ],
  "upsert": true
}
```
{{< /expandable >}}


### Add Hierarchy scope

The hierarchy scope establishes the calendar hierarchy that the Rhize calendar service uses to establish [calendar precedence]({{< relref "about-calendars-and-overrides" >}}).


**Requirements.**
A calendar hierarchy scope must have the following properties.
- A time zone.
- An ID that starts with the prefix `WorkCalendar_`

To associate equipment with the hierarchy scope, add the equipment items to the `equipmentHierarchy`.
To create levels of calendar scope, add `children`, each of which can link to equipment.


**Example**


This example adds a work-calendar hierarchy, `WorkCalendar_PSDT`, with associated children scopes.
The scope and its children link to equipment created in the previous step through `equipmentHierarchy`.

{{< expandable title=" mutation addHierarchyScope" >}}

  
```gql
mutation AddHierarchyScope($input: [AddHierarchyScopeInput!]!) {
  addHierarchyScope(input: $input) {
    numUids
  }
}
{
  "input": [
    {
      "effectiveStart": "2024-05-29T00:00:00Z",
      "id": "WorkCalendar_PSDT.Scope A",
      "label": "WorkCalendar_PSDT.Scope A",
      "timeZoneName": "Europe/London",
      "equipmentHierarchy": {
        "id": "Equipment A",
        "version": "1"
      },
      "children": [
        {
          "effectiveStart": "2024-05-29T00:00:00Z",
          "id": "WorkCalendar_PSDT.Scope A.Scope B",
          "label": "Scope B",
          "timeZoneName": "Europe/London",
          "equipmentHierarchy": {
            "id": "Equipment B",
            "version": "1"
          },
          "children": [
            {
              "effectiveStart": "2024-05-29T00:00:00Z",
              "id": "WorkCalendar_PSDT.Scope A.Scope B.Scope C",
              "label": "Scope C",
              "timeZoneName": "Europe/London",
              "equipmentHierarchy": {
                "id": "Equipment C",
                "version": "1"
              },
            }
          ]
        },
        {
          "effectiveStart": "2024-05-29T00:00:00Z",
          "id": "WorkCalendar_PSDT.Scope A.Scope D",
          "label": "Scope D",
          "timeZoneName": "Europe/London",
          "equipmentHierarchy": {
            "id": "Equipment D",
            "version": "1"
          },
        }
      ]
    },
    {
      "effectiveStart": "2024-05-29T00:00:00Z",
      "id": "WorkCalendar_PDOT.Scope A",
      "label": "WorkCalendar_PDOT.Scope A",
      "timeZoneName": "Europe/London",
      "equipmentHierarchy": {
        "id": "Equipment A",
        "version": "1"
      },
      "children": [
        {
          "effectiveStart": "2024-05-29T00:00:00Z",
          "id": "WorkCalendar_PDOT.Scope A.Scope B",
          "label": "Scope B",
          "timeZoneName": "Europe/London",
          "equipmentHierarchy": {
            "id": "Equipment B",
            "version": "1"
          },
          "children": [
            {
              "effectiveStart": "2024-05-29T00:00:00Z",
              "id": "WorkCalendar_PDOT.Scope A.Scope B.Scope C",
              "label": "Scope C",
              "timeZoneName": "Europe/London",
              "equipmentHierarchy": {
                "id": "Equipment C",
                "version": "1"
              },
            }
          ]
        },
        {
          "effectiveStart": "2024-05-29T00:00:00Z",
          "id": "WorkCalendar_PDOT.Scope A.Scope D",
          "label": "Scope D",
          "timeZoneName": "Europe/London",
          "equipmentHierarchy": {
            "id": "Equipment D",
            "version": "1"
          },
        }
      ]
    }
  ]
}
```

{{< /expandable >}}

### Create work calendar definition

After you have created equipment and hierarchy scopes, create a `workCalendarDefinition`.
The calendar service reads the entries to create records of machine states.

**Requirements:**
The work calendar definition must have the following:
- An associated work calendar
- A label. Note that Rhize **uses the to label to configure overrides**.
- At least one entry that has at least these properties:
    - Start date
    - type (one of: `PlannedDowntime`, `PlannedShutdown`, and `PlannedBusyTime`).
    - A recurrence time interval in the representation defined by the [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) standard
  
You can optionally add `properties` to each entry to add additional context and information.
  
**Naming conventions**
- When you name the ID, the recommended convention is `{CalendarDefinition.Label}.{HierarchyScope.id}`. This convention helps readers quickly understand which scopes and equipment its entries affect.

  
**Example**

This `addWorkCalendarDefinition` mutation adds entries for planned downtime and shutdown time.
Note that the calendar definitions link to a hierarchy scope defined in the previous step.

{{< expandable title="mutation addWorkCalendarDefinition" >}}
```gql
mutation AddWorkCalendarDefinition($input: [AddWorkCalendarDefinitionInput!]!, $upsert: Boolean) {
  addWorkCalendarDefinition(input: $input, upsert: $upsert) {
    numUids
  }
}
{
  "input": [
    {
      "id": "PDOT C.Scope C",
      "label": "PDOT C",
      "workCalendars": [
        {
          "id": "PDOT C.Scope C",
          "label": "PDOT C.Scope C"
        }
      ],
      "hierarchyScope": {
        "id": "WorkCalendar_PDOT.Scope A.Scope B.Scope C"
      },
      "entries": [
        {
          "id": "PDOT C.Scope C.1",
          "label": "PDOT C.Scope C.1",
          "durationRule": "PT15M",
          "startRule": "2024-05-29T09:30:00Z",
          "recurrentTimeIntervalRule": "R/P1D",
          "properties": [
            {
              "id": "PDOT C.Scope C.1.PropA",
              "label": "Prop A",
              "value": "1"
            },
            {
              "id": "PDOT C.Scope C.1.PropB",
              "label": "Prop B",
              "value": "2"
            }
          ],
          "entryType": "PlannedDowntime"
        },
        {
          "id": "PDOT C.Scope C.2",
          "label": "PDOT C.Scope C.2",
          "durationRule": "PT1H5M",
          "startRule": "2024-05-29T08:45:00Z",
          "recurrentTimeIntervalRule": "R/P1D",
          "properties": [
            {
              "id": "PDOT C.Scope C.2.PropA",
              "label": "Prop A",
              "value": "3"
            },
            {
              "id": "PDOT C.Scope C.2.PropB",
              "label": "Prop B",
              "value": "4"
            }
          ],
          "entryType": "PlannedDowntime"
        }
      ]
    },
        {
      "id": "PSDT D.Scope D",
      "label": "PSDT D",
      "workCalendars": [
        {
          "id": "PSDT D.Scope D",
          "label": "PSDT D.Scope D"
        }
      ],
      "hierarchyScope": {
        "id": "WorkCalendar_PSDT.Scope A.Scope D"
      },
      "entries": [
        {
          "id": "PSDT D.Scope D.1",
          "label": "PSDT D.Scope D.1",
          "durationRule": "PT1H",
          "startRule": "2024-05-29T13:00:00Z",
          "recurrentTimeIntervalRule": "R/P1D",
          "properties": [
            {
              "id": "PSDT D.Scope D.1.PropA",
              "label": "Prop A",
              "value": "1"
            },
            {
              "id": "PSDT D.Scope D.1.PropB",
              "label": "Prop B",
              "value": "2"
            }
          ],
          "entryType": "PlannedShutdown"
        },
        {
          "id": "PSDT D.Scope D.2",
          "label": "PSDT D.Scope D.2",
          "durationRule": "PT2H",
          "startRule": "2024-05-29T12:00:00Z",
          "recurrentTimeIntervalRule": "R/P1D",
          "properties": [
            {
              "id": "PSDT D.Scope D.2.PropA",
              "label": "Prop A",
              "value": "3"
            },
            {
              "id": "PSDT D.Scope D.2.PropB",
              "label": "Prop B",
              "value": "4"
            }
          ],
          "entryType": "PlannedShutdown"
        }
      ]
    },
    {
      "id": "PDOT D.Scope D",
      "label": "PDOT D",
      "workCalendars": [
        {
          "id": "PDOT D.Scope D",
          "label": "PDOT D.Scope D"
        }
      ],
      "hierarchyScope": {
        "id": "WorkCalendar_PDOT.Scope A.Scope D"
      },
      "entries": [
        {
          "id": "PDOT D.Scope D.1",
          "label": "PDOT D.Scope D.1",
          "durationRule": "PT3H",
          "startRule": "2024-05-29T18:00:00Z",
          "recurrentTimeIntervalRule": "R/P1D",
          "properties": [
            {
              "id": "PDOT D.Scope D.1.PropA",
              "label": "Prop A",
              "value": "1"
            },
            {
              "id": "PDOT D.Scope D.1.PropB",
              "label": "Prop B",
              "value": "2"
            }
          ],
          "entryType": "PlannedDowntime"
        },
        {
          "id": "PDOT D.Scope D.2",
          "label": "PDOT D.Scope D.2",
          "durationRule": "PT2H",
          "startRule": "2024-05-29T21:00:00Z",
          "recurrentTimeIntervalRule": "R/P1D",
          "properties": [
            {
              "id": "PDOT D.Scope D.2.PropA",
              "label": "Prop A",
              "value": "3"
            },
            {
              "id": "PDOT D.Scope D.2.PropB",
              "label": "Prop B",
              "value": "4"
            }
          ],
          "entryType": "PlannedDowntime"
        }
      ]
    }
  ],
  "upsert": true
}
```
{{< /expandable >}}

