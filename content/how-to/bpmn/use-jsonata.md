---
title: 'Use JSONata'
date: '2024-05-10T16:20:35-03:00'
categories: ["how-to"]
description: A guide with examples of how to use JSONata in Rhize
weight:
menu:
  main:
    parent: howto-bpmn
    identifier:
---


To handle JSON payloads as they pass through {{< abbr "process variable context" >}} and from system to system,
Rhize uses the JSONata expression language.
JSONata is a query language to filter, transform, and create JSON objects.
In BPMN workflows, JSONata expressions have two essential functions:
- **Map data.** That is, to move values from one data structure to another.
- **Calculate data.** That is, to receive values as input and create new data from them.

This guide details how to use JSONata in your Rhize environment and provides some examples relevant to manufacturing workflows.
For the full details of the JSONata expression language, read the [Official JSONata documentation](https://docs.jsonata.org/overview.html).

## Use JSONata in Rhize

JSONata returns the final value of its expression as output.
This output can be of any data type that JSON supports.
Generally, we recommend outputting a JSON object with the keys and values of the data you want to subsequently work with.

In practice, creating an expression usually follows these steps:
1. Begin with an `=`.
1. Embed the expression in parenthesis.
1. At the top of expression, write your logic, variables, and functions.
1. At the bottom of the expression, create a JSON object whose keys are names you configure and whose values are derived from your logic.

For example:

{{% tabs %}}
{{% tab "expression" %}}
```JSON
=( 
  $logic := "Hello" & " " & "World";

  {
    "output": $logic
  }
)
```
{{% /tab %}}
{{% tab "Output" %}}
```JSON
{
  "output": "Hello World"
}
```

{{% /tab %}}
{{% /tabs %}}



### Begin each expression with a `=`

Note that the previous expression begins with the character for an equals sign, `=`.
This sign instructs Rhize to parse the subsequent data as JSONata (as opposed to raw JSON or some other data structure).

### Access root variable context with `$.`

To access the root of the entire BPMN variable space,
use the dollar character followed by a dot, `$.`.
For example, this expression accesses all IDs for an `equipmentClass` object from the root variable context, `$.`.

{{% tabs %}}

{{% tab "expression" %}}
**Expression:** 
`$.equipmentClass.id`
{{% /tab%}}

{{% tab "Full transformation" %}}
**Expression:** 
`$.equipmentClass.id`


**Input:**
```json
{
  "equipmentClass": [
    {
      "id": "Vessel-A012",
      "description": "Stock Solution Vessel",
      "effectiveStart": "2023-05-24T09:58:00Z",
      "equipmentClassProperties": [
        {
          "id": "Volume",
          "description": "Vessel Volume"
        }
      ]
    },
    {
        "id": "Vessel-A013",
        "description": "Stock Solution Vessel"
    }
  ]
}

```

**Output:**
```
[
  "Vessel-A012",
  "Vessel-A013"
]
```
{{% /tab %}}


{{% /tabs %}}

### Use JSONata in service tasks

JSONata can be used in many Rhize BPMN tasks.

Particularly, the JSONata service task exists to receive input and pass it to another element or system at a later BPMN element.
The main parameters of the JSONata task are as follows:
- **Input JSON**. The incoming JSON. Alternatively, use the `=` character to write JSONata directly in the input field, accessing the process variable context through `$.`
- **Expression**. The expression to further transform the input JSON and write to the output variable. If the node already uses JSONata in its input, enter a `$` (no period).
- **Output variable**. The name of the variable to pass to the process variable context.


Though JSONata tasks are the most common use of JSONata,
other task parameters can also use JSON if they begin with the  `=` prefix.

- API payloads. Calls to the Rhize GraphQL API and to external APIs can both use JSONata in their payloads.
- Outgoing payloads. The messages you send with intermediate message throws can have JSONata.
- Response transformations.
  - REST. Write JSONata in the input expression to filter data. This is helpful to remove data from overfetching and to keep the process variable size lower.
  - GraphQL input. As with JSONata, your GraphQL tasks can pass their results to JSONata. This is less common, as GraphQL already provides tight control of the data it returns, but it is useful for calculating derived values.

Review the full list of parameters that accept JSONata in the [BPMN element reference]({{< relref "bpmn-elements" >}}).

### JSONata version

Many implementations of JSONata exist.
Rhize uses a custom Go implementation for high performance and safe calculation.
If you are ever wondering about whether a JSONata expression is compatible with the version Rhize uses, test it in the JSONata playground that is built-in to Rhize.

## JSONata examples

These snippets provide some examples of JSONata from manufacturing workflows.
To experiment with how they work, copy the data and expression into the JSONata explorer and try changing values.

### Filter for items that contain

This expression returns all `equipmentActual` items that are associated with a job response, `JR-4`.

{{% tabs %}}

{{% tab "Expression" %}}

`$.data.queryJobResponse[id="JR-4"].equipmentActual`
{{% /tab %}}

{{% tab "Example transformation" %}}
**Expression:**
`$.data.queryJobResponse[id="JR-4"].equipmentActual`

**Input:**
```json
{
  "data": {
    "queryJobResponse": [
      {
        "id": "JR-1",
        "data": [
          {
            "value": 100
          }
        ],
        "equipmentActual": [
          {
            "id": "hauler"
          },
          {
            "id": "actuator-121"
          }
        ]
      },
      {
        "id": "JR-5",
        "data": [
          {
            "value": 103.2
          }
        ],
        "equipmentActual": [
          {
            "id": "actuator-122"
          },
          {
            "id": "actuator-13"
          }
        ]
      },
      {
        "id": "JR-2",
        "data": [],
        "equipmentActual": [
          {
            "id": "actuator-13"
          }
        ]
      },
      {
        "id": "JR-4",
        "data": [
          {
            "value": "101.8"
          }
        ],
        "equipmentActual": [
          {
            "id": "actuator-132"
          },
          {
            "id": "actuator-133"
          }
        ]
      },
      {
        "id": "JR-3",
        "data": [],
        "equipmentActual": [
          {
            "id": "actuator-091"
          }
        ]
      },
      {
        "id": "JR-12",
        "data": [],
        "equipmentActual": []
      },
      {
        "id": "JR-123",
        "data": [],
        "equipmentActual": [
          {
            "id": "actuator-121"
          }
        ]
      },
      {
        "id": "JR-6",
        "data": [],
        "equipmentActual": []
      },
      {
        "id": "JR-8",
        "data": [
          {
            "value": "96.7"
          }
        ],
        "equipmentActual": [
          {
            "id": "actuator-091"
          }
        ]
      },
      {
        "id": "JR-9",
        "data": [],
        "equipmentActual": []
      },
      {
        "id": "JR-10",
        "data": [
          {
            "value": "105.0"
          }
        ],
        "equipmentActual": [
          {
            "id": "actuator-12"
          }
        ]
      },
      {
        "id": "JR-7",
        "data": [
          {
            "value": "103.2"
          }
        ],
        "equipmentActual": []
      }
    ]
  }
}
```
**Output:**
```json
[
  {
    "id": "actuator-132"
  },
  {
    "id": "actuator-133"
  }
]
```
{{% /tab %}}
{{% /tabs %}}

### Find actual associated with high values

This expression finds all Job responses whose `value` exceeds `100`.
It outputs the matching job response IDs along with their associated equipment actuals.


```
$map($.data.queryJobResponse, function($v,$i,$a){
    $number($v.data.value) > 102
        ?  {"jobResponseId": $v.id, "EquipmentActual": $v.equipmentActual}
        }
    )

```

{{% tabs %}}
{{% tab "input" %}}
```json
{
  "data": {
    "queryJobResponse": [
      {
        "id": "JR-1",
        "data": [
          {
            "value": 100
          }
        ],
        "equipmentActual": [
          {
            "id": "hauler"
          },
          {
            "id": "actuator-121"
          }
        ]
      },
      {
        "id": "JR-5",
        "data": [
          {
            "value": 103.2
          }
        ],
        "equipmentActual": [
          {
            "id": "actuator-122"
          },
          {
            "id": "actuator-13"
          }
        ]
      },
      {
        "id": "JR-2",
        "data": [],
        "equipmentActual": [
          {
            "id": "actuator-13"
          }
        ]
      },
      {
        "id": "JR-4",
        "data": [
          {
            "value": "101.8"
          }
        ],
        "equipmentActual": [
          {
            "id": "actuator-132"
          },
          {
            "id": "actuator-133"
          }
        ]
      },
      {
        "id": "JR-3",
        "data": [],
        "equipmentActual": [
          {
            "id": "actuator-091"
          }
        ]
      },
      {
        "id": "JR-12",
        "data": [],
        "equipmentActual": []
      },
      {
        "id": "JR-123",
        "data": [],
        "equipmentActual": [
          {
            "id": "actuator-121"
          }
        ]
      },
      {
        "id": "JR-6",
        "data": [],
        "equipmentActual": []
      },
      {
        "id": "JR-8",
        "data": [
          {
            "value": "96.7"
          }
        ],
        "equipmentActual": [
          {
            "id": "actuator-091"
          }
        ]
      },
      {
        "id": "JR-9",
        "data": [],
        "equipmentActual": []
      },
      {
        "id": "JR-10",
        "data": [
          {
            "value": "105.0"
          }
        ],
        "equipmentActual": [
          {
            "id": "actuator-12"
          }
        ]
      },
      {
        "id": "JR-7",
        "data": [
          {
            "value": "103.2"
          }
        ],
        "equipmentActual": [
          {
            "id": "actuator-12"
          }
        ]
      }
    ]
  }
}
```

{{% /tab %}}
{{% tab "output" %}}
```json
[
  {
    "jobResponseId": "JR-5",
    "EquipmentActual": [
      {
        "id": "actuator-122"
      },
      {
        "id": "actuator-13"
      }
    ]
  },
  {
    "jobResponseId": "JR-10",
    "EquipmentActual": [
      {
        "id": "actuator-12"
      }
    ]
  },
  {
    "jobResponseId": "JR-7",
    "EquipmentActual": [
      {
        "id": "actuator-12"
      }
    ]
  }
]

```

{{% /tab %}}
{{% /tabs %}}



### Map event to operations event

This function takes data from an external weather API
and maps it onto the `operationsEvent` ISA-95 object.
It takes the earliest value from the event time data as the start, and last value as the end.
If no event data exists, it outputs a message.

```
$count(events[0]) > 0
    
    ? events.{
    "id":id,
    "description":title,
    "hierarchyScope":{
        "id":"Earth",
        "label": Earth,
        "effectiveStart": $sort(geometries.date)[0]

    },
    "category":categories.title,
    "recordTimestamp": $sort(geometries.date)[0],
    "effectiveStart": $sort(geometries.date)[0],
    "effectiveEnd": $sort(geometries.date)[$count(geometries.date)-1],
    "source": sources.id & " " & sources.url,
    "operationsEventDefinition": {
    "id": "Earth event",
    "label": "Earth event"
    }
    }

: "No earth events lately" 
```

{{% tabs %}}
{{% tab "Input" %}}

```json
{
  "description": "Natural events from EONET.",
  "events": [
    {
      "categories": [
        {
          "id": 12,
          "title": "Volcanoes"
        }
      ],
      "description": "",
      "geometries": [
        {
          "coordinates": [
            -70.8972,
            -16.345
          ],
          "date": "2024-05-06T00:00:00Z",
          "type": "Point"
        }
      ],
      "id": "EONET_6516",
      "link": "https://eonet.gsfc.nasa.gov/api/v2.1/events/EONET_6516",
      "sources": [
        {
          "id": "SIVolcano",
          "url": "https://volcano.si.edu/volcano.cfm?vn=354020"
        }
      ],
      "title": "Ubinas Volcano, Peru"
    },
    {
      "categories": [
        {
          "id": 15,
          "title": "Sea and Lake Ice"
        }
      ],
      "description": "",
      "geometries": [
        {
          "coordinates": [
            -33.27,
            -51.88
          ],
          "date": "2024-02-16T00:00:00Z",
          "type": "Point"
        },
        {
          "coordinates": [
            -32.82,
            -51.09
          ],
          "date": "2024-03-01T00:00:00Z",
          "type": "Point"
        },
        {
          "coordinates": [
            -30.95,
            -51.21
          ],
          "date": "2024-03-07T00:00:00Z",
          "type": "Point"
        },
        {
          "coordinates": [
            -27.71,
            -50.88
          ],
          "date": "2024-03-15T00:00:00Z",
          "type": "Point"
        },
        {
          "coordinates": [
            -32.82,
            -51.09
          ],
          "date": "2024-03-22T00:00:00Z",
          "type": "Point"
        },
        {
          "coordinates": [
            -27.33,
            -52.45
          ],
          "date": "2024-03-29T00:00:00Z",
          "type": "Point"
        },
        {
          "coordinates": [
            -25.79,
            -52.41
          ],
          "date": "2024-04-19T00:00:00Z",
          "type": "Point"
        },
        {
          "coordinates": [
            -25.18,
            -51.86
          ],
          "date": "2024-04-26T00:00:00Z",
          "type": "Point"
        },
        {
          "coordinates": [
            -22.85,
            -52.56
          ],
          "date": "2024-05-02T00:00:00Z",
          "type": "Point"
        }
      ],
      "id": "EONET_6513",
      "link": "https://eonet.gsfc.nasa.gov/api/v2.1/events/EONET_6513",
      "sources": [
        {
          "id": "NATICE",
          "url": "https://usicecenter.gov/pub/Iceberg_Tabular.csv"
        }
      ],
      "title": "Iceberg D28A"
    }
  ],
  "link": "https://eonet.gsfc.nasa.gov/api/v2.1/events",
  "title": "EONET Events"
}
```
{{% /tab %}}

{{% tab "Output" %}}
```json
[
  {
    "id": "EONET_6516",
    "description": "Ubinas Volcano, Peru",
    "hierarchyScope": {
      "id": "Earth",
      "effectiveStart": "2024-05-06T00:00:00Z"
    },
    "category": "Volcanoes",
    "recordTimestamp": "2024-05-06T00:00:00Z",
    "effectiveStart": "2024-05-06T00:00:00Z",
    "effectiveEnd": "2024-05-06T00:00:00Z",
    "source": "SIVolcano https://volcano.si.edu/volcano.cfm?vn=354020",
    "operationsEventDefinition": {
      "id": "Earth event",
      "label": "Earth event"
    }
  },
  {
    "id": "EONET_6513",
    "description": "Iceberg D28A",
    "hierarchyScope": {
      "id": "Earth",
      "effectiveStart": "2024-02-16T00:00:00Z"
    },
    "category": "Sea and Lake Ice",
    "recordTimestamp": "2024-02-16T00:00:00Z",
    "effectiveStart": "2024-02-16T00:00:00Z",
    "effectiveEnd": "2024-05-02T00:00:00Z",
    "source": "NATICE https://usicecenter.gov/pub/Iceberg_Tabular.csv",
    "operationsEventDefinition": {
      "id": "Earth event",
      "label": "Earth event"
    }
  }
]

```

{{% /tab %}}
{{% /tabs %}}

### Calculate summary statistics

These functions calculates statistics for an array of numbers.
Some of the output uses built-in JSONata functions, such as `$max()`.
Others, such as the ones for median and standard deviation,
are created in the expression.
  
```
(
$stdPop := function($arr) {
    (
        $variance := $map($arr, function($v, $i, $a) {
            $power($v - $average($a),2)
            });
        $sum($variance) / $count($arr) ~> $sqrt()
      
    )}; 

$median := function($arr) {
    (
        $sorted := $sort($arr);
        $length := $count($arr);
        $mid := $floor($length/2);
        $length % 2 = 0
            ? $median := ($sorted[$mid-1] + $sorted[$mid]) /2
            : $median := $sorted[$mid];
    )}; 

 {
 "std_population":$stdPop($.data.arr),
 "mean":$average($.data.arr),
 "median":$median($.data.arr),
 "mode": $max($.data.arr)
 }
)
```
{{% tabs %}}
{{% tab "input" %}}
```json
{
  "data":{"arr":[1,3,5,7,9,11,50]}
}
```
{{% /tab %}}
{{% tab "output" %}}
```json
{
  "std_population": 15.71818133531,
  "mean": 12.28571428571,
  "median": 7,
  "mode": 50
}
```
{{% /tab %}}
{{% /tabs %}}

### Select random item

This expression randomly selects an item from the plant's array of available equipment, then makes that item the `equipmentRequirement` for a segment in a job response.

```json
(

$randomChoice := function($a) {
    (
        $selection :=
            $random() * ($count($a)+1) ~> $floor();
        $a[$selection]
      
    )}; 

{ 
"segmentRequirement": {
  "workRequirement": {"id": $.PO},
  "equipmentRequirements":[$randomChoice($.available)],
  "id": "Make widget"
  }
}

)
```
{{% tabs %}}
{{% tab "Input" %}}
```json
{
  "available":["line_1","line_2","line_3","line_4","line_5"],
  "PO":"po-123"
  }
```
{{% /tab %}}

{{% tab "Output" %}}
```json
{
  "segmentRequirement": {
    "workRequirement": {
      "id": "po-123"
    },
    "equipmentRequirements": [
      "line_2"
    ],
    "id": "Make widget"
  }
}
```
{{% /tab %}}
{{% /tabs %}}

<!-- Uncomment and add sample data
### Recursively descend into values

A recursive function to calculate IDs of sub lots made from a parent sublot.


```typescript
(
    $next := function($x, $y) {$x > 1 ? 
        (
            $substring($y[-1],-1) = "Z" ?
            $next($x - 1, $append($y, $y[-1] & '1')) :
            $next($x - 1, $append(
                $y,
                 $substring($y[-1],0,$length($y[-1])-1) & $substring($substringAfter("123456789ABCEFHJKLNORTUVYZ",$substring($y[-1],-1)),0,1)
            ))
        )
        : $y};
    {
        "children": $next(n, [nextId])
    }
)
```

 -->
