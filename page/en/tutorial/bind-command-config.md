---
lang: en
title: "Configure Command"
layout: single
permalink: /en/docs/bind-commnad-config/
date: 2024-08-14T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"

# breadcrumbs: true
---
'Bindcommand' is a bundle of associated 'MetaView' and works as an execute() method.

### Key Features

- Flow control: Flow control can be performed with a step-by-step callback function.
- Add column: Add 'HTML Column' object (column reference value)
- Response Management : Binds the server response to 'MetaView'.
- Validation : Validates with column constraint settings.
### Key Properties

- 'outputOption' : This is a method of calling output (MetaView) depending on the option.
- 'url' : The path 'axios.url'.
- 'config' : 'axios' config object.
- 'valid' : This is a validation 'MetaView'.
- 'bind' : This is the 'MetaView' request from the server.
- 'output' : This is the server's response 'MetaView'. 
- '\_outputs' : A collection that controls 'MetaView' associated with the output.
- '\_Model' : Point to 'BindModel' object (owner)

### Callback (Properties)

- 'cbBegin' : This is the callback function before the start.
- 'cbValid' : Validation callback function.
- 'cbBind' : This is the server request callback function.
- 'cbResult' : This is the server response callback function.
- 'cbOutput' : Output callback function.
- 'cbEnd' : This is the callback function before termination.

### Events

- 'onExecute' : Invoke for the first time when executing execute().
- 'onExecuted' : Last call when executing execute().
- 
### Key Methods

- execute(): Execute the 'Bindcommand' object.
- addColumn(): Add 'HTMLColumn' object.
- addColumnValue(): Add 'HTMLColumn' object and set 'value' value.
- setColumn(): Register the specified column as a reference in 'MetaView'.
- release() : Unreferences to the specified MetaView.
- newOutput(): Add the response output 'MetaView' (\_outputs)
- removeOptput(): Remove the output 'MetaView' (\_outputs)

## Setting up a per-command server request

### Request path by command

'url' is the url path you request from the server. If there is no value, it will be replaced by 'baseUrl'.

```js
var bm = new BindModel();

bm.addCommand('cmdA');
bm.addCommand('cmdB');

bb.command['cmdA'].url = '/user/1';
bb.command['cmdB'].url = '/list';
```
- command 'cmdA' and 'cmdB' have different paths.

#### Set dynamic url

Sometimes it is necessary to dynamically change the request path (url) depending on a specific command. It can be easily set up through the callback function of the BindModel object.
 ```js
 var idx = 1;
 bb.command['cmdA'].cbBegin = function(cmd) {
	 cmd.url = `/user/${idx}`;
 };
```
### Request configuration (axios)

Set the config of the axios for http communication.
#TODO


## Setting up events by command

Command-by-command event setting is a feature that allows you to perform various tasks by calling specific events when you execute the execute() method.

타입 : onExecute, onExecuted
```ts
// Event Type
type onExecute = (model: BindModel, cmd: BindCommand) => void; 
type onExecuted = (model: BindModel, cmd: BindCommand) => void; 
```
- onExecute : Called the first time the execute() method is executed.
- onExecuted —Last called after the execute() method is executed.

These event types take the BindModel and Bindcommand objects as factors, allowing you to perform a variety of tasks.

```js
var bm = new BindModel();

bm.addCommand('read');
// Global Event Settings
bm.onExecute = function() { 
    console.log('model 에서 onExecute 호출');
};
bm.onExecuted = function() { 
    console.log('model 에서 onExecuted 호출');
};
// Command Event Settings
bm.command['read'].onExecute = function() { 
    console.log('command 에서 onExecute 호출');
};
bm.command['read'].onExecuted = function() { 
    console.log('command 에서 onExecuted 호출');
};
// Execute
bm.command['read'].execute();

// Output results:
// Call onExecute from model
// Call onExecute from command
// Call onExecuted from command
// Call onExecuted in model
```

Setting global and command-specific events allows you to process a variety of tasks in sequence when the execute() method is executed, which is useful for defining the initialization or subsequent actions required when certain tasks are executed.


## To control flow by command

When executing the execute() method, callback functions are called sequentially to control the flow. 
[[41. Callback Lifecycle-B|Refer to: Callback Lifecycle]

#### Callback function's call order
1. cbBegin : URL and config information settings
2. cbValid : Validation
3. cbBind : Call before server request
4. Performing a server request
5. cbResult : Call after server response
6. cbOutput : Response processed with \_outputs collection
7. cbEnd : Call after completion of the entire process

### 1. Callback at start

The first callback to be called upon execution().

Type: cbBegin
```ts
type cbBegin = (cmd: BindCommand) => void;
```

The general application plan is
- Utilized for setting up information in url and config.

```js
var bm = new BindModel();
// Add Commands
bm.addCommand('list');
// Callback Settings
bm.command['list'].cbBegin = (command) => { 
    command.url = '/member/1'; 
};
```
### 2. Validation callback

Callback called before validation for 'valid(MetaView)' when executing execute(). 
If the return value is false, a cbFail callback is called and execution is terminated.
Type: cbValid
```ts
type cbValid = (valid: MetaView, cmd: BindCommand) => boolean;
```

The general application plan is
- Used for inspection before requesting a server.
- It is used to check the processing results from the user.

```js
var bm = new BindModel();
// Add Commands
bm.addCommand('list');
// Callback Settings
bm.command['list'].cbValid = function(view) { 
	return view.colums.count <= 0 
};
bm.command['list'].cbValid = function() {
	return confirm ('Do you want to delete?')
};
```

### 3. Server Request Callback

Callback called before sending the 'bind(MetaView)' column value when executing execute().

Type: cbBind
```ts
type cbBind = (view: MetaView, cmd: BindCommand, config: object) => void;
```

The general application plan is
- Used to set the transport type. (encttype)
- Utilized for integrated login-related settings.
- Used for password encryption.

### 4. Server Response Callback

Callback called after receiving a response from the server when executing execute().
Type: cbResult
```ts
type cbResult = (data: object, cmd: BindCommand, res: object) => object;
```

The general application plan is
- It is used to customize the responded data into a schema in the form of 'MetaView'.

```js
// data = { aa: 1, bb: 2 }
bm.command['list'].cbResult = function(data) {
	return = {
		rows: data
	};
};
```
- Return value: '{aa: 1, bb:2}'
### 5. Output callback

Callback called after the response is read into the '\_outputs' collection.

Depending on the 'Bindcommand' output option, the way data is imported into the '\_outputs' collection is different.
[24. Bind Command Configuration - B# Type of Output Option (output) | Reference: Type of Output Option]

Type: cbOutput
```ts
type cbOutput = (
	views: MetaViewColleciton, 
	cmd: BindCommand, 
	res: object
) => void;
```

The general application plan is
- It is utilized for screen (html) binding using the 'output(MetaView)' responded.

```json
{
	"rows": [
		{ "u_name": "Neo", "gender", "M" },
		{ "u_name": "Jane", "gender", "W" },
	]
}
```

```js
bm.command['list'].cbOutput = function(views) {
	for(var i = 0; i < views[0].rows.count; i++) {
		var row = views['first'].rows[i];
		console.log(1, row['u_name'], row['gender']);
	}
};
// Output results:
// 0  Neo  M
// 1  Jane  W
```
'views[0]' is the same as 'output' of 'Bindcommand'.

### 6. Callback at the end of the day

The last callback to be called when execute().

Type: cbEnd
```ts
type cbEnd = (status: number, cmd: BindCommand, res: object) => void; 
```

The general application plan is
- Use it to deliver success messages.
- Utilizes for path redirection.
- Utilizes the *execute()* chain connection of other commands.

```js
bm.command['list'].cbEnd = function(views) {
	alert('Normal processed')
}
```

Each callback function is used to coordinate the behavior of commands in certain situations, or to pre-process and post-process data. This provides detailed control over the execution flow of Bindcommand.


## Setting Output Options

Additional 'MetaView' can be specified in addition to the 'MetaView' provided in 'Bindcommand'.

타입 : outputOption
```ts
type outputOption = { option: number, index: number }`; 
```
- The initial value of the object type is 'outputOption = {option: 0, index: 0}.
- The 'option' attribute is a method of fetching data from 'output(MetaView)' depending on the value.
- The 'index' property specifies the location of the rows index when setting the value of the column. (Option: Used if 3)
- 'outOpt' is an alias for 'outputOptions'.

### Configuration via Creator

You can set the output option parameter by passing it when you create an object.

```js
var bm = new BindModel();

var bc1 = new BindCommand(bm, 1);
var bc2 = new BindCommand(bm, { option: 1, index: 1 });
```
- The value of 'bc1.outputOption' is '{option:1, index: 0}.
- The value of 'bc2.outputOption' is '{option:1, index:1}'.

### setting in addcommand()

The addcommand() method of the 'BindModel' object can deliver 'outputOptions' as parameters when created.

```js
var bm = new BindModel();

bm.addCommand('read', 2);
bm.addCommand('view', { option: 2, index: 1 });
```
- `bm.command['read'].outputOption` value `{option:2, index: 0}`
- `bm.command['view'].outputOption` vaule `{option:2, index: 1}` 

### Set to Property

It can be set by changing the 'outputOption' property of the 'Bindcommand' object.

```js
bm.command['read'].outputOption = 3;
bm.command['view'].outputOption = { option: 3, index: 1 };
```
- `bm.command['read'].outputOption` value `{option:3, index: 0}`
- `bm.command['view'].outputOption` value `{option:3, index: 1}`
## Setting Validation Targets (valid)

'valid(MetaView)' must be configured for validation.
First, add the inspection object to the 'valid.column' collection and set the 'required' and 'constraits' properties.

Type: constructs (HTML Column property)
```ts
type RegExpType = {reg: RegExp, msg: string, return: boolean = true};
type FuncType = (value: any)=>boolean;
type ContiditionType = RegExpType | FuncType;
type ConstrainstType = ContiditionType[] | ContiditionType;

const constraints: ConstrainstType[] | ConstrainstType;
```
- required : indicates whether required; if true, the value 'value' is 'blank, null' will fail.
- constructs : Inspects for constraints if the value is not blank or null.
	• reg : regular expression to be matched.
	• msg : If return is false depending on whether it matches or not, it is a failure message.
		• If return is true, this is an error message when matching fails.
		• If return is false, it is an error message when matching is successful.
	• return : This is the return result for reg matching. The default is true.

#### Constraints by frequency of use

| **FREQUIRED** | **required** | **constraits** | **Explanation**|
| ------ | ------------ | --------------- | --------------------------- |
| 50% || || Select value
| 30% | true | | required value
| 15% | | | {...} | Select value, value constrained |
| 5% | True | {...} | Required value, value has constraints |
 (Shopping mall table: based on 20ea)

```js
var bm = new BindModel();

bm.addCommand('test');

bm.cmd['test'].addColumn('user_name');
bm.cmd['test'].addColumn('passwd');
bm.cmd['test'].addColumn('user_id');
bm.cmd['test'].addColumn('email');

bm.columns['user_name'].required = true;
bm.columns['passwd'].required = true;
bm.columns['user_id'].required = true;

bm.columns['passwd'].constraints = {
	regex: /.{6}/, msg: "Please enter at least 6 characters."
};
bm.columns['user_id'].constraints = [
	(val)=>{ reutrn val.length > 8 }, 
	{regex: /\D{8}/, msg: "Please type in English", return: false}
];
bm.columns['email'].constraints = {
	regex: /.{6}/, msg: "Please enter at least 6 characters."
};

bm.cmd['test'].execute();
```
- user_name : Mandatory condition. Failed to enter blank space. 'cbFail' will be called.
- passwd : Required condition, successful only if it matches the regular expression condition.
- user_id : prerequisite, successful in the first functional condition, and successful only when non-matching in the second regular expression condition.
- email : Selection condition; successful if blank; successful if you enter a value must be matched with the regular expression.

## To add a column

Adding a column adds the column to the default table '\_baseTable' and registers the reference to the 'MetaView' specified in the bind command. Methods of adding a column include addColumn() and addColumnValue().

타입 : addColumn(), addColumnValue()
```ts
type addColumn = (
	colName: string, 
	views?: string | string[], 
	bTable?: string | MetaTable
) => BindCommand;

type addColumnValue = (
	colName: string, 
	value: any, 
	views?: string | string[], 
	bTable?: string | MetaTable
) => BindCommand;
```
- If you use the '$all' indicator in the views parameter, it is added to all 'MetaView (valid, bind, output).
- The bTable parameter specifies the table to which the column is added, and the default is '\_baseTable'.

### Adding and mapping columns

Add a column to the base table and register a reference to the specified 'MetaView'. The addColumn() method adds an empty column, and the addColumnValue() method sets the 'value' value after adding the column.

```js
var bm = new BindModel();

bm.addCommand('test');

bm.command['test'].addColumn('aa', 'valid');
bm.command['test'].addColumn('bb', ['bind', 'output']);
bm.command['test'].addColumn('cc', '$all');
bm.command['test'].addColumn('dd');

// bm['first'].columns.count  == 4 ('aa', 'bb', 'cc', 'dd')

// bm.command['test'].valid.columns.count  == 3 ('aa', 'cc', 'dd')
// bm.command['test'].bind.columns.count   == 3 ('bb', 'cc', 'dd')
// bm.command['test'].output.columns.count == 3 ('bb', 'cc', 'dd')
```
- Invoking the addColumn() method adds an empty column.
- Calling the addColumnValue() method sets the 'value' value after adding the column.
- The 'aa' column registers a reference to the specified 'MetaView (valid).
- The 'bb' column registers a reference to the specified 'MetaView (bind, output).
- The 'cc' column registers a reference to the entire 'MetaView (valid, bind, output).
- If you omit the parameter when adding the 'dd' column, register the reference in the entire 'MetaView'.

### Adding and mapping columns to an extension table

Add a column to the specified 'MetaTable' and register a reference to the specified 'MetaView'.

```js
var bm = new BindModel();

bm.addTable('second');
bm.addCommand('test');

bm.command['test'].addColumn('aa', 'valid', bm.second);
bm.command['test'].addColumn('bb', '$all', 'second');

// bm['first'].columns.count  == 0
// bm['second'].columns.count == 2 ('aa', 'bb')

// bm.command['test'].valid.columns.count  == 1 ('aa', 'bb')
// bm.command['test'].bind.columns.count   == 1 ('bb')
// bm.command['test'].output.columns.count == 1 ('bb')
```
- The 'aa' column is added to the 'MetaTable(second)' and registers a reference to the specified 'MetaView(valid).
- The 'bb' column is added to 'MetaTable(second)' and the reference is registered in the entire 'MetaView'.

You can use the above methods to add tables and columns in a variety of ways, and map each column to the appropriate 'MetaView', which is useful for efficiently handling data validation, binding, and output.

## Setting Up an Existing Column

You can register or release an already registered column as a reference to 'MetaView', which allows you to dynamically manage the column to meet various requirements such as validation, binding, and output of data.

#### Key Features Summary
-  Column Settings: You can set a specific table's column to the desired MetaView to process data validation, binding, and output.
- Uncolumn: You can dynamically manage column references by releasing the set column from the desired MetaView.

타입 : setColumn(), release()
```ts
type TableNameType : string;
type ColumnType : string;
type FullColumnType : TableNameType + '.'+ ColumnType;
type ColumnDotType : FullColumnType | ColumnType;
type ViewType : 'valid' | 'bind' | 'output' | '$all' | string;

type setColumn = (
	colName: ColumnDotType, 
	views: ViewType | ViewType[], 
	bTable?: string | MetaTable
) => void;

type release = (
	colName: string, 
	views?: ViewType | ViewType[]
) => void;
```

### Setting up and releasing columns

When setting up columns, you can specify columns for a specific table using the 'Table Name + . + Column Name' notation.

```js
var bm = new BindModel();
bm.addTable('second');

// Add a column to the default table, first
bm.addColumn('aa');
bm.addColumn('bb');

// Adding a column to the extension table, second
bm['second'].columns.add('cc');
bm['second'].columns.add('dd');

bm.addCommand('test');

// Column Settings
bm.command['test'].setColumn('aa', 'valid');
bm.command['test'].setColumn(['bb', 'second.cc'], 'bind');
bm.command['test'].setColumn('second.dd', '$all');

// Number of columns in the base table
// bm.first.columns.count == 2 ('aa', 'bb')
// Number of columns in the extension table
// bm.second.columns.count == 2 ('cc', 'dd')
// Number of columns in MetaView
// bm.command['test'].valid.columns.count == 2 ('aa', 'dd')
// bm.command['test'].bind.columns.count == 2 ('bb', 'dd')
// bm.command['test'].output.columns.count == 2 ('cc', 'dd')

// Release the column
bm.command['test'].release('aa', 'valid');
bm.command['test'].release(['bb', 'cc'], 'bind');
bm.command['test'].release('dd', '$all');

// Number of columns in MetaView
// bm.command['test'].valid.columns.count == 0
// bm.command['test'].bind.columns.count == 0
// bm.command['test'].output.columns.count == 0
```
- Add the 'aa' column and register the reference to the specified 'MetaView (valid).
- Add a 'bb' column and register a reference to the specified 'MetaView(bind).
- Add 'cc' column 'MetaTable(second)' and register the reference to the specified 'MetaView(bind).
- Add 'dd' column 'MetaTable(second)' and register a reference to the entire 'MetaView'.

The above methods allow you to dynamically set up or release existing columns in MetaView, providing flexibility for complex data processing requirements.

## Type of output option (output)

If response data is multiple records, import the data in the order of the '\_outputs' collection. To import multiple records, you must add a collection of '\_outputs' as many records as possible. 
Records greater than '\_outputs' collections are excluded. (option > 0)

| Options | Description |
| ----------- | --------------------------------------------------------------- |
| 0 (default) | output bind 안함                                                  |
| 1 | Gets the row of all columns
| 2 | Gets the row of existing columns |
| 3 | Gets the row of existing columns and sets the row of index locations to column value |
### Ignore output: option = 0

Does not import data to 'output(MetaView)'. It is mainly used to modify data, such as create, update, delete, and is suitable when response data is not needed. 
This option is mainly used when performing data change operations, and increases efficiency by minimizing data returns.

```js
var bm = new BindModel();
// Add 'create' command and set output option to 0 to not get data
bm.addCommand('create', 0);
// Set a value in column 'aa'
bm.cmd['create'].addColumnValue('aa', -10);
// Execute a command
bm.cmd['create'].execute();

// bm.columns.count == 1
// bm.columns['aa'].value == -10
// bm.cmd['create'].output.rows.count == 0
```


### Import all data: option = 1 

Option value 1 is a way to load all response data into 'output(MetaView)', which is useful for simply outputting a column of all data received from a server or for checking the data structure. 

```js
{
	"rows": [
		{ "aa": 10, "bb", 20, "cc": 30 },
		{ "aa": 11, "bb", 21, "cc": 31 },
	]
}
```

```js
var bm = new BindModel();

// Add 'list' command and set output option to 1 to get all data
bm.addCommand('list', 1);

bm.cmd['list'].execute();

// bm.columns.count == 3
// bm.cmd['list'].output.rows.count == 2
// bm.cmd['list'].output.rows[0]['aa'] == 10
// bm.cmd['list'].output.rows[0]['bb'] == 20
// bm.cmd['list'].output.rows[0]['cc'] == 30
// bm.cmd['list'].output.rows[1]['aa'] == 11
// bm.cmd['list'].output.rows[2]['bb'] == 21
// bm.cmd['list'].output.rows[3]['cc'] == 31
```
- Even if the column is not predefined, it is dynamically generated based on response data.

This option is useful for checking the full structure of data received from the server, or for loading basic data for display and subsequent processing operations.

### Import only data from the specified column: option = 2 

From the response data, only columns existing in 'output(MetaView)' are imported.

```js
{
	"rows": [
		{ "aa": 10, "bb", 20, "cc": 30 },
		{ "aa": 11, "bb", 21, "cc": 31 },
	]
}
```

```js
var bm = new BindModel();
bm.addCommand('list', 2);
bm.cmd['list'].addColumnValue('aa', 0);
bm.cmd['list'].addColumnValue('bb', 0);
bm.cmd['list'].execute();

// bm.columns.count == 2
// bm.cmd['list'].output.rows.count == 2
// bm.cmd['list'].output.rows[0]['aa'] == 10
// bm.cmd['list'].output.rows[0]['bb'] == 20
// bm.cmd['list'].output.rows[1]['aa'] == 11
// bm.cmd['list'].output.rows[2]['bb'] == 21
```

### Set data to column value (specified column): option = 3

From the response data, only the column that exists in 'output(MetaView)' is imported, and the specified data is set to the value of the column 'value'.

```json
{
	"rows": { "aa": 10, "bb", 20, "cc": 30 },
}
```

```js
var bm = new BindModel();
bm.addCommand('read', 3);
bm.cmd['read'].addColumnValue('aa', 0);
bm.cmd['read'].addColumnValue('bb', 0);
bm.cmd['read'].execute();

// bm.columns.count == 2
// bm.columns['aa'].value == 10
// bm.columns['bb'].value == 20
```

#### Single Indexing (option: 3)

Sets the specific 'rows' value of the response data to the column 'value'.

```json
{
	"rows": [
		{ "aa": 10, "bb", 20, "cc": 30 },
		{ "aa": 11, "bb", 21, "cc": 31 },
	]
}
```

```js
var bm = new BindModel();
bm.addCommand('test', 3);
bc.outputOption.index = 1;   // index set
bm.cmd['test'].addColumnValue('aa', 0);
bm.cmd['test'].addColumnValue('bb', 0);
bm.cmd['test'].execute();

// bm.columns.count == 2
// bm.columns['aa'].value == 11
// bm.columns['bb'].value == 21
```
- The value of the second (index = 1) 'rows' is set to the value of 'value'.

#### Specifying composite index (option: 3)

Sets a specific 'rows' value for multiple response data to the column 'value'.

```json
[
	{
		"rows": [
			{ "aa": 10, "bb", 20, "cc": 30 },
			{ "aa": 11, "bb", 21, "cc": 31 },
		]
	},
	{
		"rows": [
			{ "dd": 40, "ee", 50 },
			{ "dd": 41, "ee", 51 },
			{ "dd": 42, "ee", 52 },
		]
	},
]
```

```js
var bm = new BindModel();
bm.addCommand('test', 3);
bc.outputOption.index = [0, 1];   // index set
bm.cmd['test'].newOutput('two');
bm.cmd['test'].addColumnValue('aa', 0);
bm.cmd['test'].addColumnValue('bb', 0);
bm.cmd['test'].addColumnValue('dd', 0);
bm.cmd['test'].execute();

// bm.columns.count == 3
// bm.columns['aa'].value == 10
// bm.columns['bb'].value == 20
// bm.columns['dd'].value == 41
// bm.cmd['test'].output.rows.count == 2
// bm.cmd['test'].two.rows.count == 3
```
- The value of index = 0 in the first response record was set to the value of the column ('aa', 'bb').
- The value of index = 1 in the second response record was set to the value of column ('dd').
```json
{
	"rows": { "aa": 10, "bb", 20, "cc": 30 },
}
```

```js
var bm = new BindModel();
bm.addCommand('read', 3);
bm.cmd['read'].addColumnValue('aa', 0);
bm.cmd['read'].addColumnValue('bb', 0);
bm.cmd['read'].execute();

// bm.columns.count == 2
// bm.columns['aa'].value == 10
// bm.columns['bb'].value == 20
```

#### Single Indexing (option: 3)

Sets the specific 'rows' value of the response data to the column 'value'.

```json
{
	"rows": [
		{ "aa": 10, "bb", 20, "cc": 30 },
		{ "aa": 11, "bb", 21, "cc": 31 },
	]
}
```

```js
var bm = new BindModel();
bm.addCommand('test', 3);
bc.outputOption.index = 1;   // index set
bm.cmd['test'].addColumnValue('aa', 0);
bm.cmd['test'].addColumnValue('bb', 0);
bm.cmd['test'].execute();

// bm.columns.count == 2
// bm.columns['aa'].value == 11
// bm.columns['bb'].value == 21
```
- The value of the second (index = 1) 'rows' is set to the value of 'value'.

#### Specifying composite index (option: 3)

Sets a specific 'rows' value for multiple response data to the column 'value'.

```json
[
	{
		"rows": [
			{ "aa": 10, "bb", 20, "cc": 30 },
			{ "aa": 11, "bb", 21, "cc": 31 },
		]
	},
	{
		"rows": [
			{ "dd": 40, "ee", 50 },
			{ "dd": 41, "ee", 51 },
			{ "dd": 42, "ee", 52 },
		]
	},
]
```

```js
var bm = new BindModel();
bm.addCommand('test', 3);
bc.outputOption.index = [0, 1];   // index set
bm.cmd['test'].newOutput('two');
bm.cmd['test'].addColumnValue('aa', 0);
bm.cmd['test'].addColumnValue('bb', 0);
bm.cmd['test'].addColumnValue('dd', 0);
bm.cmd['test'].execute();

// bm.columns.count == 3
// bm.columns['aa'].value == 10
// bm.columns['bb'].value == 20
// bm.columns['dd'].value == 41
// bm.cmd['test'].output.rows.count == 2
// bm.cmd['test'].two.rows.count == 3
```
- The value of index = 0 in the first response record was set to the value of the column ('aa', 'bb').
- The value of index = 1 in the second response record was set to the value of column ('dd').