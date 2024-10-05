---
lang: en
title: "Configure BindModel"
layout: single
permalink: /en/docs/bind-model-config/
date: 2024-08-14T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"

# breadcrumbs: true
---
'BindModel' is the core class of the framework, and its main functions include adding commands, adding columns, adding tables, and injecting service objects.

### Key Features

- Add command: Add 'Bindcommand' object.
- Add Column: Add 'HTML Column' object.
- Add Table: Add 'MetaTable' object.
- Provides basic callback function—Provides a step-by-step basic callback function.
- Inject service object: To construct 'BindModel' object by injecting service object.

### Key Properties

- 'baseConfig' : the config setting object of the default axios.
- 'url' : Path to 'baseConfig.url'. 
- 'column' : A collection that controls 'HTML Column'.
- 'command' : This is a collection that controls 'Bindcommand' (aka cmd)
- 'fn' : A collection that controls user functions.
- 'items' : A collection that controls the items. (Primary properties of the column)
- '\_tables' : A collection that controls 'MetaTable'.
- '\_baseTable': Default table. ('column' = \_baseTable.column)

### Events

- 'onExecute' : Commonly called when executing execute() for the first time.
- 'onExecuted' : Commonly called last when executing execute().

### Callback (Properties)

- 'cbBaseBegin' : This is the default pre-start callback function.
- 'cbBaseValid' : This is the default validation callback function.
- 'cbBaseBind' : This is the default server request callback function.
- 'cbBaseResult' : This is the default server response callback function.
- 'cbBaseOutput' : Default output callback function.
- 'cbBaseEnd' : This is the callback function before the default termination.
### Key Methods

- addcommand(): Create 'Bindcommand' object.
- addTable(): Add 'MetaTable' object.
- setService(): Construct the 'BindModel' object by injecting the service object.
- checkSelector(): DOM validation with 'selector' object.
- getSelector(): Gets the list of 'selector' objects.
- addColumn(): Add 'HTMLColumn' object.
- addColumnValue(): Add 'HTMLColumn' object and set 'value' value.
- setMapping(): Map 'HTMLColumn' to 'MetaView' object in 'Bindcommand'
- init(): Run 'preRegister', 'preCheck', and 'preRedy' callback functions sequentially.

'BindModel' is a multi-purpose framework that provides the flexibility to add and manage a variety of commands, tables, and columns. This document helps you understand the key features of this class and callbacks and use them effectively.

## Setting up a server request

### Default Request Path

The url attribute sets the default URL path that requests from the server. If you set the url path for the Bindcommand object, the url path for the BindModel is ignored.

```js
var bm = new BindModel();

bm.url  = '/user';
```

#### Set dynamic url

If you need to dynamically change the request path according to a specific command, you can easily set up URLs using the callback function of the BindModel object.

 ```js
 var idx = 1;
 bm.cbBegin = function(cmd) {
	cmd._model.url = `/user/${idx}`;
 };
```
- 'cmd._model' refers to the BindModel with Bindcommand.

### Default Request Environment Settings (axios)

You can configure the request environment through the default settings in axios for HTTP communication. 

```js
var bm = new BindModel();
// Axios Preference Example
bm.baseConfig.baseURL = 'https://api.example.com';
bm.baseConfig.timeout = 10000; // 10초
bm.baseConfig.headers.common['Authorization'] = 'Bearer YOUR_TOKEN';
bm.baseConfig.headers.post['Content-Type'] = 'application/json';
```
- baseURL —Set the default URL to be common to all requests.
- timeout : Sets the maximum wait time for a request.
- headers —Set the default header to use on request; for example, you can set the authentication token or specify the type of content.


## Setting up the Run Event

Global event that is called upon execution() of all commands.
타입 : onExecute, onExecuted
```ts
type onExecute = (model: BindModel, cmd: BindCommand) => void; 
type onExecuted = (model: BindModel, cmd: BindCommand) => void; 
```
- The 'onExecute' event is called for the first time on all exec() runs.
- The 'onExecuted' event is last called at all exec() runs.

```js
var bm = new BindModel();
bm.addCommand('read');
// Global Event Settings
bm.onExecute = () => { 
	console.log('model 에서 onExecute 호출')
};
bm.onExecuted = () => { 
	console.log('model 에서 onExecuted 호출')
};
// Execute
bm.command['read'].execute();

// Output results:
// Call onExecute from model
// Call onExecuted in model
```
## Handling errors and failures

### error handling

Callback called upon all errors and exceptions.
Type: cbError
```ts
type cbError = (msg: string, status: number, response: object) => void;
```

The cbError occurrence time is
- Called in case of an axios error or ajax error related to a server request.
- Called in case of all errors and exceptions.

```js
var bm = new BindModel();

bm.cbError = function(msg, status, res) { 
	console.error('Err: '+ msg); 
};
```
- The default value of 'cbError' is provided at the time of object creation.
### Dealing with Failure

Processes logical failure messages (mainly used in the event of a validation failure)
Type: cbFail
```ts
type cbFail = (msg: string, valid: MetaView) => void;
```

When "cbFail" occurred, the execute() method was executed
-  Called when the 'valid(MetaView)' validation fails.
- Called when a false return is made from the 'cbValid' / 'cbBaseValid' callback function.

```js
var bm = new BindModel();

bm.cbFail = function(msg, valid) { 
	console.warn ('Failed. Err:'+ msg); 
};
```
- The default value of 'cbFail' is provided at the time of object creation.


## Flow control (hooking)

When execute(), the callback function can be called sequentially to control the flow. 

[[41. Callback Lifecycle-B|Refer to: Callback Lifecycle]

### 1. Callback at base start

The first callback to be called upon execution().
Target 'cbBegin' has higher priority than callback 'cbBaseBegin'.
Type: cbBase Begin
```ts
type cbBaseBegin = (cmd: BindCommand) => void;
```

 The general application plan is
- Utilized for setting common information in url and config.

```js
var bm = new BindModel();

bm.cbBaseBegin = function(cmd) {
	cmd.url = '/member/1';
};
```
### 2. Basic Validation Callback

Callback called before validation for 'valid(MetaView)' when executing execute().
Destination 'cbValid' has higher priority than callback 'cbBaseValid'.
If the return value is false, the callback 'cbFail' is called and terminated.
Type: cbBaseValid
```ts
type cbBaseValid = (valid: MetaView, cmd: BindCommand) => boolean;
```

The general application plan is
- Used for inspection before requesting a server.
- It is used to check the processing results from the user.

```js
var bm = new BindModel();

bm.cbBaseValid = function(view, cmd) { 
	return view.colums.count <= 0;
};
bm.cbBaseValid = function(view, cmd) {
	return confirm ('Do you want to delete?');
};
```

### 3. Default Server Request Callback

Callback called before sending the 'bind(MetaView)' column value when executing execute().
Target 'cbBind' has higher priority than callback 'cbBaseBind'.

Type: cbBaseBind
```ts
type cbBaseBind = (view: MetaView, cmd: BindCommand, config: object) => void;
```

The general application plan is
- Used to set the transport type. (encttype)
- Utilized for integrated login-related settings.
- Used for password encryption.

### 4. Default Server Response Callback

Callback called after receiving a response from the server when executing execute().
Destination 'cbResult' has higher priority than callback 'cbBaseResult'.
타입 : cbBaseResult
```ts
type cbBaseResult = (data: object, cmd: BindCommand, res: object) => object;
```

The general application plan is
- It is used to change the responded data to a schema in the form of 'MetaView'.

```js
var bm = new BindModel();
// data = { aa: 1, bb: 2 }
bm.cbBaseResult = function(data) {
	return {
		rows: data
	};
};
```
- Return value: '{aa: 1, bb:2}'

### 5. Default Output Callback

Callback called after the response is read into the \_outputs collection.
Destination 'cbOutput' has higher priority than callback 'cbBaseOutput'.
Depending on the 'Bindcommand' output option, the data and the '\_outputs' collection are imported differently.

[24. Bind Command Configuration - C# Type of Output Option (output) | Reference: Type of Output Option]

타입 : cbBaseOutput
```ts
type cbBaseOutput = (views: MetaViewColleciton, cmd: BindCommand, res: object) => void; 
```

The general application plan is
- It is utilized for screen (html) binding using the 'output(MetaView)' responded.

```json
{
	"rows": [
		{ "u_name": "Hong Gildong", "gender", "M" },
		{ "u_name": "Sungchunhyang", "gender", "W" },
	]
}
```

```js
var bm = new BindModel();

bm.cbBaseOutput = function(views) {
	// views[0] instanceof MetaView
	for(var i = 0; i < views[0].rows.count; i++) {
		var row = views['first'].rows[i];
		console.log(i, row['u_name'], row['gender']);
	}
}
// 0 Hong Gil-dong M
// 1. Sung Chunhyang W
```

[[42. Multi-view (output)-C | Reference: Multi-view (output)]
### 6. Callback at Default Termination

The last callback to be called when execute().
Target 'cbEnd' has higher priority than callback 'cbBaseEnd'.

Type: cbBaseEnd
```ts
type cbBaseEnd = (status: number, cmd: BindCommand, res: object) => void; 
```

The general application plan is
- Use it to deliver success messages.
- Utilizes for path redirection.
- Use to link the execute() chain of other commands.

```js
var bm = new BindModel();

bm.cbBaseEnd = function(views) {
	alert('Normal Processed');
};
```


## To add a command

Create a 'Bindcommand' object by invoking the addcommand() method.
The 'Bindcommand' object is the processing unit of the bound model and contains the 'MetaView' of the 'valid', 'bind', and 'output' attributes.

Type: addcommand()
```ts
function addCommand(cmdName: string, outOpt?: number = 0, bTable?: string | MetaTable): BindCommand;
```
- Specify the alias for 'Bindcommand' of 'cmdName' and add it as an output option. (Default outOpt = 0)
- If bTable is specified, '\_baseTable' of the added 'Bindcommand' is set.

```js
var bm = new BindModel();

bm.addCommand('create');
bm.addCommand('read', 3);

// bm.command['create'] instanceof BindCommand
// bm.command['read'] instanceof BindCommand
// bm.command['create'].outputOption == 0 
// bm.command['read'].outputOption == 3 
```
- The default value for outOpt is '0'.
- The 'Bindcommand' object you added can be accessed by the 'command' collection.
- 'command' provides a 'cmd' alias.

Example: Specify the Default Table
```js
var bm = new BindModel();

bm.addTable('second');

bm.addCommand('list', 1, 'second');
bm.addCommand('edit', 3, bm.second);

// bm.command['list']._baseTable === bm.second
// bm.command['edit']._baseTable === bm.second
```
- If 'MetaTable' is specified when 'Bind Command' is added, 'baseTable' is set for all 'MetaView'.


## To add a column

Add a column.

타입 : addColumn(), addColumnValue()
```ts
type addColumn = (
	colName: string, 
	cmds?: string | string[], 
	views?: string | string[], 
	bTable?: string | MetaTable
) => BindCommand;

type addColumnValue = (
	colName: string, 
	value: any, 
	cmds?: string | string[], 
	views?: string | string[], 
	bTable?: string | MetaTable
) => BindCommand;
```
- omitting cmds, views adds columns to the base table.
- If you specify cmds and views, the reference is registered in MetaView in 'command'.
- If you use the '$all' indicator in the cmds parameter, it is added to all 'command'.
- 'views[0]' is the same as 'output' of 'Bindcommand'.

### Add Column

Add a column to the main table (\_baseTable).

```js
var bm = new BindModel();
// Add Column
bm.addColumn('aa');
bm.addColumnValue('bb', 'man');

// bm.columns['aa'].value  == ''
// bm.columns['bb'].value  == 'man'
// bm._baseTable === bm._tables['first'] === bm._tables[0] === bm.first
```
- A column named 'aaa' is added by invoking the addColumn() method.
- The addColumnValue() method is called to add a column named 'bbb' and the value 'man' is set.
- You can add 'bTable' to the specified 'MetaTable' column. (Default: '\_baseTable')
### Adding and mapping columns

Add a column to the base table and map it to the target 'Bindcommand'.

```js
var bm = new BindModel();
// command generation 
bm.addCommand('cmd1');
bm.addCommand('cmd2');
bm.addCommand('cmd3');
// Add Column
bm.addColumn('aa', 'cmd1');
bm.addColumn('bb', ['cmd2', 'cmd3'], ['valid', 'bind']);
bm.addColumn('cc', '$all', 'output');

// bm['first'].columns.count  == 3 ('aa', 'bb', 'cc')

// bm.command['cmd1'].valid.columns.count  == 1 ('aa')
// bm.command['cmd1'].bind.columns.count   == 1 ('aa')
// bm.command['cmd1'].output.columns.count == 2 ('aa', 'cc')

// bm.command['cmd2'].valid.columns.count  == 1 ('bb')
// bm.command['cmd2'].bind.columns.count   == 1 ('bb')
// bm.command['cmd2'].output.columns.count == 1 ('cc')

// bm.command['cmd3'].valid.columns.count  == 1 ('bb')
// bm.command['cmd3'].bind.columns.count   == 1 ('bb')
// bm.command['cmd3'].output.columns.count == 1 ('cc')
```
- Add 'Bindcommand' with the name specified by the addcommand() method.
- The column named 'aaa' is mapped to all 'MetaView' in the specified (cmd1).
- The column named 'bbb' maps to the specified (cmd2, cmd3) 'MetaView (valid, bind'
- Columns named 'cc' are mapped to the 'MetaView (output)' of the whole (cmd1, cmd2, cmd3).

### Adding and mapping columns to tables you have added

Add a column to the specified 'MetaTable' and map it to the target 'Bind Command'.

```js
var bm = new BindModel();
// Adding Tables and Commands
bm.addTable('second');
bm.addCommand('cmd1');
// Adding and mapping columns
bm.addColumn('aa', 'cmd1', 'valid');
bm.addColumn('bb', 'cmd1', 'bind', 'second');

// bm['first'].columns.count  == 1 ('aa')
// bm['second'].columns.count == 1 ('bb')

// bm.command['cmd1'].valid.columns.count  == 1 ('aa')
// bm.command['cmd1'].bind.columns.count   == 1 ('bb')
// bm.command['cmd1'].output.columns.count == 0
```
- As an addTable() method, add 'MetaTable' to the '\_tables' collection.
- Add 'Bindcommand' with the name specified by the addcommand() method.
- Columns named 'aaa' are added to the base table and mapped to the specified 'MetaView (valid).
- The column named 'bbb' is added to 'second(MetaTable)' and mapped to the specified 'MetaView(bind).
### Add Column

Add a column to the main table (\_baseTable).

```js
var bm = new BindModel();
// Add Column
bm.addColumn('aa');
bm.addColumnValue('bb', 'man');

// bm.columns['aa'].value  == ''
// bm.columns['bb'].value  == 'man'
// bm._baseTable === bm._tables['first'] === bm._tables[0] === bm.first
```
- A column named 'aaa' is added by invoking the addColumn() method.
- The addColumnValue() method is called to add a column named 'bbb' and the value 'man' is set.
- You can add 'bTable' to the specified 'MetaTable' column. (Default: '\_baseTable')
### Adding and mapping columns

Add a column to the base table and map it to the target 'Bindcommand'.

```js
var bm = new BindModel();
// command generation 
bm.addCommand('cmd1');
bm.addCommand('cmd2');
bm.addCommand('cmd3');
// Add Column
bm.addColumn('aa', 'cmd1');
bm.addColumn('bb', ['cmd2', 'cmd3'], ['valid', 'bind']);
bm.addColumn('cc', '$all', 'output');

// bm['first'].columns.count  == 3 ('aa', 'bb', 'cc')

// bm.command['cmd1'].valid.columns.count  == 1 ('aa')
// bm.command['cmd1'].bind.columns.count   == 1 ('aa')
// bm.command['cmd1'].output.columns.count == 2 ('aa', 'cc')

// bm.command['cmd2'].valid.columns.count  == 1 ('bb')
// bm.command['cmd2'].bind.columns.count   == 1 ('bb')
// bm.command['cmd2'].output.columns.count == 1 ('cc')

// bm.command['cmd3'].valid.columns.count  == 1 ('bb')
// bm.command['cmd3'].bind.columns.count   == 1 ('bb')
// bm.command['cmd3'].output.columns.count == 1 ('cc')
```
- Add 'Bindcommand' with the name specified by the addcommand() method.
- The column named 'aaa' is mapped to all 'MetaView' in the specified (cmd1).
- The column named 'bbb' maps to the specified (cmd2, cmd3) 'MetaView (valid, bind'
- Columns named 'cc' are mapped to the 'MetaView (output)' of the whole (cmd1, cmd2, cmd3).

### Adding and mapping columns to tables you have added

Add a column to the specified 'MetaTable' and map it to the target 'Bind Command'.

```js
var bm = new BindModel();
// Adding Tables and Commands
bm.addTable('second');
bm.addCommand('cmd1');
// Adding and mapping columns
bm.addColumn('aa', 'cmd1', 'valid');
bm.addColumn('bb', 'cmd1', 'bind', 'second');

// bm['first'].columns.count  == 1 ('aa')
// bm['second'].columns.count == 1 ('bb')

// bm.command['cmd1'].valid.columns.count  == 1 ('aa')
// bm.command['cmd1'].bind.columns.count   == 1 ('bb')
// bm.command['cmd1'].output.columns.count == 0
```
- As an addTable() method, add 'MetaTable' to the '\_tables' collection.
- Add 'Bindcommand' with the name specified by the addcommand() method.
- Columns named 'aa' are added to the base table and mapped to the specified 'MetaView (valid).
- The column named 'bb' is added to 'second(MetaTable)' and mapped to the specified 'MetaView(bind).

## To add a table

Add 'MetaTable' as an addTable() method.

Type: addTable()
```ts
type addTable (tableName: string) => MetaTable;
```

The general application plan is
- Utilizes changes to the default table.
- Use to create tables to change \_baseTable.
- Use when designating as a reference table for 'MetaView' in 'command'.

```js
var bm = new BindModel();
// step A
bm.addTable('second');
bm.addTable('three');
// step B
bm._baseTable = bm.second;  // baseTable 설정
bm.addColumn('aa');
// step C
bm.addCommand('cmd1', 0, 'three');
bm.command['cmd1'].addColumn('bb');

// bm['first'] === bm._tables['first'] === bm._tables[0]
// bm['second'] === bm._tables['second']
// bm['three'] === bm._tables['three']

// bm['first'].columns.count  == 0
// bm['second'].columns.count == 1 ('aa')
// bm['three'].columns.count  == 1 ('bb')

// bm.command['cmd1']._baseTable === bm.second

// bm.command['cmd1'].valid.columns.count  == 1 ('bb')
// bm.command['cmd1'].bind.columns.count   == 1 ('bb')
// bm.command['cmd1'].output.columns.count == 1 ('bb')
```
- A: 'MetaTable' is added to the '\_tables' collection and 'BindModel' objects in the addTable() method.
- B: We changed the base table to 'second(MetaTable)' by specifying '\_baseTable'.
- C: The addcommand() method designated '\_baseTable' of 'cmd1' as 'three'.

## Setting global items (global column)

'items' has raw information from the column.

The general application plan is
- When injecting service objects into 'BindModel', it is used to manage the global information of the column.
- Utilizes a column when it is shared across multiple tables.
- It is a 'selector' attribute and is used for DOM validation.

### Add Item

As a way to add items, you can add them from the items collection and from the service object.

[25. Service Object Configuration - C# Item Area Configuration | Refer to: Service Object Configuration]
Type: items.add()
```ts
type ColumnType = {
	selector: SelectorType,
	getter: ()=>any,
	setter: (val)=>any,
	getFilter: ()=>any,
	setFilter: (val)=>any,
	default: stirng | number | boolean | null, // 기본값
	value: any,
	alias: string,
	caption: string,
	constraints: ConstrainstType,  // 제약조건
	required: boolean | false, // required
};
type ValueType = string | number | boolean | ColumnType;

// items.add()
type add = (itemName: string, iType: ValueType) => void;
```

```js
var bm = new BindModel();
// Add Item
bm.items.add('col1', 1);
bm.items.add('col2', '');
bm.items.add('col3', { columnName: 'newCol3'});
bm.items.add('col4', {selector: { key:'#ID', type: 'value'}});
// Create as item column
bm._readItem();
```
- Add to the item named 'col1' and set the value '{value:1}'.
- Add to the item named 'col2' and set the value '{value: '}'.
- Add to the item named 'col3' and set the value '{columnName: 'newCol3'}..
- Add to the item named 'col4' and set the value '{selector: {key:'#ID', type: 'value'}'
- Calling the \_readItem() method creates an item as a column in the default table.

### Item Validation

 The checkSelector() method allows you to check whether the DOM of the selector property in the items collection is valid.
타입 : checkSelector()
```ts
type checkSelector = (
	collection?: PropertyCollection = this.items, 
	isLog: boolean = false
) => string[];
```
- The default value of the 'collection' parameter is the collection of 'this.items'.
- 'collection' can be designated as items, columns, '[table name]'.column, 'valid.column', 'bind.column', and 'output.column'.
- If you set isLog = true, the 'key' value of the failed 'selector' is displayed in the console window.

```js
var bm = new BindModel();
// Add Item
bm.items.add('item1', {selector: { key:'#user_name', type: 'value'}});
bm.items.add('item2', {selector: { key:'.sub_name', type: 'text'}});
bm.items.add('item3', {selector: { key:'input[name=gender]', type: 'none'}});
// Selector Examination
bm.checkSelector(); // Empty Array Check Successful
```
- The checkSelector() method checks whether the 'selector' value of 'items' is valid.

The preCheck callback function automatically handles DOM validation when injecting service objects.
[25. Service Object Configuration-C | Reference: Service Object Configuration]

```js
var bm = new BindModel();

bm.columns.add('item1', {selector: { key:'#user_name', type: 'value'}});
bm.columns.add('item2', {selector: { key:'.sub_name', type: 'text'}});
bm.columns.add('item3', {selector: { key:'input[name=gender]', type: 'none'}});

bm.checkSelector (bm.column, true); // List of failed selector objects
```
- Examine the DOM for the presence of the element.
  `< ... id="user_name">, \< ... class="sub_name">, \<input name="gender"... >`


## To look up the selector (selector)

Obtain a list of 'selectors' in the 'items' collection.
Type: getSelector()
```ts
type KeyType = 'none' | 'value' | 'text' | 'html' | 'prop' | 'attr' | 'css';
type SelectorType = { key: string, type: KeyType };

type getSelector = (
	collection?: PropertyCollection = this.items
) => SelectorType[];
```
- The default for the 'collection' parameter is this.items collection.

```js
var bm = new BindModel();

bm.columns.add('item1', {selector: {key:'#ID1', type: 'value'}});
bm.columns.add('item2', {selector: {key:'#ID2', type: 'text'}});

bm.getSelector();
// [{key:'#ID1', type: 'value'}, {key:'#ID2', type: 'text'}]
```
- You can obtain a list of 'selectors' for the specified collection.


