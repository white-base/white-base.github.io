---
lang: en
title: "How to create objects"
layout: single
permalink: /en/docs/create-method/
date: 2024-08-14T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"

breadcrumbs: true
---

'BindModel' offers a number of creation methods to suit the needs of the user, understand the pros and cons of each method and choose the appropriate method as needed.

### 1. Creation by service objects

Service objects can be managed separately to increase productivity. Define the items and commands needed to create objects at once.

```js
var bm = new BindModel({
	items: {
		aa: 10,
		bb: 20,
		cc: 30,
		dd: 40
	},
	command: {
		create: {},
		read: {
			outputOption: 3
		}
	},
	mapping: {
		aa: { create: 'valid'},
		bb: { read: ['bind', 'output']},
		cc: { $all: 'output'}
	}
});

// Check it out
// bm.command['create'].valid.columns.count   == 1 ('aa')
// bm.command['create'].bind.columns.count    == 0
//  bm.command['create'].output.columns.count == 1 ('cc')

// bm.command['read'].valid.columns.count    == 0
// bm.command['read'].bind.columns.count     == 1 ('bb')
// bm.command['read'].output.columns.count   == 2 ('bb','cc')
// bm.columns.count  // 3 ('aa','bb','cc')
```

### 2. Map after adding to items 

Specifies a commonly managed item, which is useful if columns are used for multiple commands.

```js
var bm = new BindModel();

// Add command
bm.addCommand('create');
bm.addCommand('read', 3);

// Add Item
bm.items.add('aa', 10);
bm.items.add('bb', 20);
bm.items.add('cc', 30);
bm.items.add('dd', 40);

// mapping
bm.setMapping({
	aa: { create: 'valid' },
	bb: { read: 'bind' },
	cc: { $all: ['output'] }   // $all = all command
});

// Check it out
// bm.command['create'].valid.columns.count  == 1 ('aa')
// bm.command['create'].bind.columns.count   == 0
// bm.command['create'].output.columns.count == 1 ('cc')

// bm.command['read'].valid.columns.count    == 0
// bm.command['read'].bind.columns.count     == 1 ('bb')
// bm.command['read'].output.columns.count   == 1 ('cc')

// bm.columns.count  == 3 ('aa','bb')

// bm.columns['aa'].value; == 10
// bm.columns['bb'].value; == 20
// bm.columns['cc'].value; == 30
```
### 3. Setting commands when adding columns

This is how you specify the command at the time of column generation. It is effective when you gradually expand the functionality.

```js
var bm = new BindModel();

// Add command
bm.addCommand('create');
bm.addCommand('read', 3);

// Add Column and Set Commands
bm.addColumn('aa', 'create', 'valid');
bm.addColumn('bb', 'read', 'bind');
bm.addColumn('cc', '$all', 'output');   

// Check it out
// bm.command['create'].valid.columns.count  == 1 ('aa')
// bm.command['create'].bind.columns.count   == 0
// bm.command['create'].output.columns.count == 1 ('cc')

// bm.command['read'].valid.columns.count    == 0
// bm.command['read'].bind.columns.count     == 1 ('bb')
// bm.command['read'].output.columns.count   == 1 ('cc')

// bm.columns.count  // 3 'aa','bb'
```

### 4. Set to command after adding column

It is a method of creating a column to be managed in advance and setting it up and using it in the necessary command. You can reduce code duplication by managing tables separately or generating common columns in advance.

```js
var bm = new BindModel();

// Add command
bm.addCommand('create');
bm.addCommand('read', 3);

// Add a column to the default columns
bm.columns.addValue('aa', 10);
bm.columns.addValue('bb', 20);
bm.columns.addValue('cc', 30);

// Set to Command
bm.command['create'].setColumn('aa', 'valid');
bm.command['create'].setColumn('cc', 'output');
bm.command['read'].setColumn('bb', ['bind']);
bm.command['read'].setColumn('cc', ['output']);

// Check it out
// bm.command['create'].valid.columns.count  == 1 ('aa')
// bm.command['create'].bind.columns.count   == 0
// bm.command['create'].output.columns.count == 1 ('cc')

// bm.command['read'].valid.columns.count    == 0
// bm.command['read'].bind.columns.count     == 1 ('bb')
// bm.command['read'].output.columns.count   == 1 ('cc')

// bm.columns.count  // 3 ('aa','bb')
```

### 5. Register column by command

It is a method of generating columns for each command, which is useful if you manage them as independent columns for each command.

```js
var bm = new BindModel();
bm.addCommand('create');
bm.addCommand('read');
bm.command['create'].addColumn('aa', 'valid');
bm.command['read'].addColumn('bb', ['bind', 'output']);

// Check it out
// bm.command['create'].valid.count  == 1 ('aa')
// bm.command['create'].bind.count   == 0
// bm.command['create'].output.count == 0

// bm.command['read'].valid.count    == 0
// bm.command['read'].bind.count     == 1 ('bb')
// bm.command['read'].output.count   == 1 ('bb')

// bm.columns.count  == 2 ('aa','bb')
```

This variety of object generation methods allow us to flexibly utilize BindModel, and it is important to consider the advantages and disadvantages of each method and choose the appropriate method.
