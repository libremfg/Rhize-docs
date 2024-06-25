---
title: 'Use work calendars'
date: '2023-09-22T14:50:39-03:00'
draft: false
categories: "how-to"
description: How to configure work calendars to account for planned and unplanned downtime in your operation.
weight: 500
menu:
  main:
    parent: how-to
    identifier: howto-work-calendars
---

Work calendars represent planned and unplanned downtime in your operation.
The Rhize API represents calendars through the `workCalendar` entity,
which has close associations with the {{< abbr "equipment" >}} and {{< abbr "hierarchy scope" >}} models.

Rhize also has a `calendar` service that periodically queries the Rhize DB for workCalendarDefinitions.
If it finds active definitions for that period, the service creates the work calendar entries and persists the data to a time-series database

{{< notice "note" >}}
Rhize's implementation of work calendars was inspired by ISO/TR
22400-10, a standard on KPIs in operations management. 
{{< /notice >}}


