---
title: "콜백 라이프사이클"
layout: single
permalink: /docs/callback-lifecycle/
date: 2011-06-23T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"
---

### execute() Lifecycle

![image-center](/assets/images/cb-diagram-2024-08-16-010115.png){: .align-center}


1. The 'onExecute' event is the first event to be called when the execute() method is executed.
2. 'cbBegin' callback is called for the first time; it is used to set the initial value.
3. Callback 'cbValid' is called before validation for validation of valid (MetaView). 
   Utilization: Use for confirm() before deletion, user validation
4. Validates columns of validation (MetaView).column.valid(value)
5. The `cbFail` callback handles logical failures.
6. 'cbBind' callback controls the http transfer style for special processing (image, integrated login)
7. bind (MetaView) requests bind.columns from the http server.
8. The 'cbResult' callback is used for processing response results.
9. Binds data and values to output (MetaView) according to outputOptions.
10. The 'cbOutput' callback performs output-related processing, such as html, as a response result or output.
11. Callback 'cbError' is called when an error or exception occurs after cbBind.
12. Callback 'cbEnd' is called last.
    Utilization: redirect, success message, other command.example() chain connection
13. The 'onExecuted' event is the last event called when the execute() method is executed.
