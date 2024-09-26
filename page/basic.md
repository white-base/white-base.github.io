---
lang: en
title: "Basic usage"
layout: single
permalink: /docs/basic/
date: 2024-08-14T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"

breadcrumbs: true
---
## Create BindModel

Creating 'BindModel' objects is the first step for data binding and server-to-server communication.
 This object serves as the key to managing AJAX communication with the server.
 
[[52. BindModel Class-B| - Reference: BindModel Configuration]
[25. Service Object Configuration-C|-Refer to: Service Object Configuration]

```js
var bm = new BindModel();

bm.url = '/user';
```
- The '/user' path is set as the default path to handle API requests related to the user's information.
- You can also set "url" for each "command".


## Add command

To add a new command to a BindModel object, use the addcommand() method, which creates a Bindcommand object and registers it with the BindModel to manage data communication with the server.

The Bindcommand object contains MetaView objects that play three key roles in data communication with the server.
- **valid**: serves to validate the data.
- **bind**: serves to bind the client's data before it is passed to the server. 
- **output** : It is responsible for fetching data received from the server.

[53. Bind Command Class-B| - Reference: Bind Command Configuration]

```js
bm.addCommand('newCmd', 3);

// bm.command['newCmd'] === bm.cmd['newCmd']
// bm.command['newCmd'] instanceof BindCommand
// bm.cmd['newCmd'].vallid instanceof MetaView
// bm.cmd['newCmd'].bind instanceof MetaView
// bm.cmd['newCmd'].output instanceof MetaView
```
- It operates differently depending on output options (range: 0, 1, 2, 3)
- You can access the 'Bindcommand' object with 'bm.command['name']'.
- 'bm.cmd['name']' refers to the same object, of which shorter 'bm.cmd' can be used as an alias.

[24. Bind Command Configuration - C# Type of Output Option | - Reference: Type of Output Option]


## Add column

The addColumn() method provides the ability to add a column to a BindModel object and set the column to the MetaView for the specified Bindcommand object. Additionally, you can use the addColumnValue() method to set the initial value of the column.

Example: Adding an Empty Column
```js
bm.addColumn('aa', 'newCmd', 'valid');
bm.addColumn('bb', 'newCmd', ['valid', 'bind']);
bm.addColumn('cc', 'newCmd', '$all');
```
- Add a column with the name 'aa' and set it to the valid (MetaView) in cmd['newCmd'].
- Add a column with the name 'bbb' and set it to 'valid', 'bind' in cmd ['newCmd'].
- Add a column with the name 'cc' and set it to the whole of the cmd ['newCmd'] ('valid', 'bind', 'output').

Example: Adding a column as an initial value
```js
bm.addColumnValue('aa', 100, 'newCmd', 'valid');
bm.addColumnValue('bb', 'B', 'newCmd', ['valid', 'bind']);
bm.addColumnValue('cc', true, 'newCmd', '$all');
```
- Add a column with the 'aa' name as the initial value of '100' and set it to 'valid' in cmd['newCmd'].
- Add a column with the initial value of 'B' under the name 'bbb' and set it to 'valid', 'bind' in cmd['newCmd'].
- Add a column with the initial value of 'true' under the name 'cc' and set it throughout cmd['newCmd'].

## Execute

The execute() method of the Bindcommand object handles three main steps: validation, data request, and data reception. Each step can be controlled through the callback function, which lets you manage the flow of requests in detail.

```js
bm.command['newCmd'].execute();
```
- There are three main steps when calling the execute() method.
	- Validation : Perform a validation of the 'valid' column, and call a 'cbFail' callback if it fails.
	- Data Binding: Request 'bind' to server path same as column.
	- Data Receipt : Gets received data as 'output'.
