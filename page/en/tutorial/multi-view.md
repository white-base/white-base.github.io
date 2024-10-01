---
lang: en
title: "Multi-View(output)"
layout: single
permalink: /en/docs/multi-view/
date: 2024-08-14T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"

breadcrumbs: true
---
Multi-view is used when data received from the server is more than one data. By default, the Bindcommand object contains the MetaView of the output property, which is a reference property for '_outputs['output1'].

This feature allows you to add or remove multiple MetaViews, each of which is used to store and manage data received from the server.

## output structure of Bindcommand

When you create a Bindcommand object, the output attribute is added by default, which is a reference to '_outputs['output1']'.

```js
var bm = new BindModel();

bm.addCommand('test');

// bm.command['test'].output == MetaView
// bm.command['test'].output1 == MetaView
```
- Here, output and output1 refer to the same MetaView.


## output (MetaView) 추가

Type: newOutput
```ts
type newOutput = (outputName?: string) => void;
```
- outputName—The name of the output to be added.

You can use the newOutput() method to add a new MetaView, which optionally takes outputName as a factor and adds MetaView to that name. 
If there is no factor, it is added by default as a name in the form of 'output + order'.

Example: Adding Output
```js
var bm = new BindModel();

bm.addCommand('test');
// bm.command['test'].output == MetaView
// bm.command['test'].output1 == MetaView
// bm.cmd['test']._outputs.count == 1

bm.cmd['test'].newOutput();
// bm.command['test'].output2 == MetaView
// bm.cmd['test']._outputs.count == 2

bm.cmd['test'].newOutput('three');
// bm.command['test'].output3 == MetaView
// bm.command['test'].three == MetaView
// bm.cmd['test']._outputs.count == 3
```
- If no factor is given in the first newOutput() method, it is added as 'output2'.
- It was added as 'three' and 'output3' in the second newOutput() method.

If you add an output MetaView and add a column to the entire command, it is also mapped to the added view.

Example: Adding a column to an entire MetaView
```js
var bm = new BindModel();

bm.addCommand('test');

bm.cmd['test'].newOutput('newOutput');
bm.cmd['test'].addColumn('aa');

// bm.cmd['test'].valid.count == 1 ('aa')
// bm.cmd['test'].bind.count == 1 ('aa')
// bm.cmd['test'].output.count == 1 ('aa')
// bm.cmd['test'].newOutput.count == 1 ('aa')
```
- The 'aa' column added to the added 'newOutput' is added.



## output (MetaView) 제거

타입 : removeOutput
```ts
type newOutput = (outputName: string) => boolean;
```
- outputName: The output name that you want to remove.

Default properties 'output', 'output1' cannot be removed.

```js
var bm = new BindModel();

bm.addCommand('test');

bm.cmd['test'].newOutput();
bm.cmd['test'].newOutput('three');

// Remove
bm.cmd['test'].removeOutput('output2');
bm.cmd['test'].removeOutput('three');
// bm.cmd['test'].removeOutput('output') : throw 발생
// bm.cmd['test']._outputs.count == 1
```
- Here, you can remove the newOutput() method and the added output2 and three.

The examples and descriptions above will help you understand how to add or remove multiple MetaViews using the multiview functionality, which is useful for managing and processing multiple data received from the server.

