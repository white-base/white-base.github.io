---
lang: en
title: "CRUD + List Example"
layout: single
permalink: /docs/crudl-example/
date: 2024-08-14T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"

# breadcrumbs: true
---

CRUDL is a concept that adds List to the traditional data processing capabilities of Create, Read, Update, and Delete. It is useful for simplifying and efficiently managing database interactions. BindModel is a useful tool for interacting with databases, aiming to design from a CRUDL perspective.

## Register (Create)

Create refers to the task of inserting new records into the database, and the REST API corresponds to a POST request.

```js
var bm = new BindModel();
// Create url settings and commands
bm.url = '/user'
bm.addCommand('create');
// Add Column
bm.addColumn('user_name', 'create', ['valid', 'bind']);
bm.addColumn('tel', 'create', 'bind');
// Execute
bm.command['create'].execute();
```
- 'bm.command['create'.valid.column' : columns subject to validation
	- Validates the "user_name" column.
- 'bm.command['create'].bind.column' : columns for HTTP transmission
	- The value of the "user_name, tel" column is sent to '/user'.
- When sending a POST request, set 'bm.baseConfig.method = 'post'.


## Read (Read)

Read refers to the task of querying a specific record in a database, and in REST API, it is a GET request.

```js
var bm = new BindModel();
// Create command
bm.addCommand('read', 3);
// Add Column
bm.addColumn('idx','read', ['valid', 'bind']);
bm.addColumn('tel','read', 'output');
// Setting and executing commands
bm.command['read'].url = '/user/1'
bm.command['read'].execute();
```
- 'bm.command['read'].valid.column' : columns subject to validation
	- Validates the "idx" column.
- 'bm.command['read'].bind.column' : columns for HTTP transmission
	- The value of the "idx" column is sent to '/user'.
- 'bm.command['read'].output.column': Columns to receive from HTTP response
	- Send the value of the "tel" column to '/user'.
- URL modification of RESTful requests can be set in cbBegin callback.


## Modified (Update)
Update refers to the task of modifying an existing record in a database, and the REST API corresponds to a PUT or PATCH request.

```js
var bm = new BindModel();
// Create url settings and commands
bm.url = '/user'
bm.addCommand('update');
// Add Column
bm.addColumn ('idx', 'update', ['valid', 'bind']); // Check Required Values
bm.addColumn('tel', 'update', 'bind');
// Execute
bm.command['update'].execute();
```
- 'bm.command['update'.valid.column' : columns subject to validation
	- Validates the "idx" column.
- 'bm.command['update'].bind.column': Columns to HTTP transmission
	- The value of the "idx, tel" column is sent to '/user'.
- When sending a PATCH request, set 'bm.baseConfig.method = 'patch'.


## Delete

Delete refers to the action of deleting a specific record in a database, and the REST API corresponds to a DELETE request.

```js
var bm = new BindModel();
// Create url settings and commands
bm.url = '/user';
bm.addCommand('delete');
// Add Column
bm.addColumn ('idx', 'delete', ['valid', 'bind']); // Required value check
// Execute
bm.command['update'].execute();
```
- 'bm.command['update'.valid.column' : columns subject to validation
	- Validates the "idx" column.
- 'bm.command['update'].bind.column': Columns to HTTP transmission
	- The value of the "idx" column is sent to '/user'.
- If sending a DELETE request, set 'bm.baseConfig.method = 'delete'.


## Inquiry (List)

List refers to the task of querying multiple records in a database, and the REST API corresponds to a GET or POST request.

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
// Create url settings and commands
bm.url = '/user/list'
bm.addCommand('list', 2);
// Add Column
bm.addColumn('user_name', 'list', 'output');
bm.addColumn('gender', 'list', 'output');
// Output callback settings
bm.command['list'].cbOutput = function(views){
	for(var i = 0; i < views[0].rows.count; i++) {
		var row = views['first'].rows[i];
		console.log(i, row['user_name'], row['gender']);
		// 0 Hong Gil-dong M
		// 1. Sung Chunhyang W
	}
}
// Execute
bm.command['list'].execute();
```
- 'bm.command['update'.valid.column' : columns subject to validation
	- Validates the "idx" column.
- 'bm.command['update'].bind.column': Columns to HTTP transmission
	- The value of the "idx" column is sent to '/user'.
- Create a screen output related task in the cbOutput callback.

As such, CRUDL provides more flexible and powerful data manipulation capabilities by adding a list to traditional CRUD capabilities. With BindModel, these CRUDL tasks are simple to perform.


## Registration / Read / Correct / Delete / Lookup Example

The following example includes the complete examples of Create, Read, Update, Delete, and List.

Server data
```json
{
	"rows": [
		{ "u_name": "Neo", "gender", "M" },
		{ "u_name": "Seri", "gender", "W" },
	]
}
```

The column was shared in the command, so you selected how to register and set the column.

Example: Method Call Method
```js
var bm = new BindModel();

// setting url
bm.url = '/user'

// command registration
bm.addCommand('create');
bm.addCommand('read', 3);
bm.addCommand('update');
bm.addCommand('delete');
bm.addCommand('list', 2);

// Register a column
bm.addColumn('user_name');
bm.addColumn('tel');
bm.addColumn('idx');
bm.addColumn('gender');

// create command setting
bm.command['create'].seColumn('user_name', ['valid', 'bind']);
bm.command['create'].seColumn('tel', 'bind');

// read command setting
bm.command['read'].seColumn('idx', ['valid', 'bind']);
bm.command['read'].seColumn('tel', 'output');
bm.command['read'].url = '/user/1';

// update command setting
bm.command['update'].seColumn('idx', ['valid', 'bind']);
bm.command['update'].seColumn('tel', 'bind');

// delete command setting
bm.command['delete'].seColumn('idx', ['valid', 'bind']);

// list command setting
bm.command['list'].seColumn('user_name', 'output');
bm.command['list'].seColumn('gender', 'output');

bm.command['list'].url = '/user/list';
bm.command['list'].cbOutput = function(views){
	for(var i = 0; i < views[0].rows.count; i++) {
		var row = views['first'].rows[i];
		console.log(i, row['user_name'], row['gender']);
		// 0 Hong Gil-dong M
		// 1. Sung Chunhyang W
	}
};

// Execute
bm.command['create'].execute();
bm.command['read'].execute();
bm.command['update'].execute();
bm.command['delete'].execute();
bm.command['list'].execute();
```

Example: Service Object Injection Method
```js
var bm = new BindModel({
	url: '/user',
	items: {
		user_name: '',
		tel: '',
		idx: '',
		gender: '',
	},
	command: {
		create: {},
		read: {
			outputOption: 3,
			url: '/user/1'
		},
		update: {},
		delete: {},
		list: {
			outputOption: 2,
			url: '/user/list',
			cbOutput = function(views){
				for(var i = 0; i < views[0].rows.count; i++) {
					var row = views['first'].rows[i];
					console.log(i, row['user_name'], row['gender']);
					// 0 Hong Gil-dong M
					// 1. Sung Chunhyang W
				}
			}
		},
	},
	mapping: {
		user_name: { 
			create: ['valid', 'bind'], 
			list: 'output' 
		},
		tel: { 
			create: 'bind', 
			read: 'bind', 
			update: 'bind'
		},
		idx: { 
			read: ['valid', 'bind'], 
			update: ['valid', 'bind'], 
			delete: ['valid', 'bind'] },
		},
		gender: { 
			list: 'output' 
		}
	}
});

// Execute
bm.command['create'].execute();
bm.command['read'].execute();
bm.command['update'].execute();
bm.command['delete'].execute();
bm.command['list'].execute();
```

These two examples illustrate how to perform CRUDL tasks efficiently. It explains how to set up and execute each command using the method call method and the service object injection method.
