---
lang: en
title: "Multi-Table"
layout: single
permalink: /en/docs/multi-table/
date: 2024-08-14T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"

# breadcrumbs: true
---

By default, the BindModel object automatically generates and uses a MetaTable named 'first', which is useful if you have the same column name but different properties or special cases.

## Add MetaTable

### Add as addTable() Method

The addTable() method allows you to add MetaTable to the \_tables collection, which automatically creates a reference key to access the table from the BindModel object.

```js
var bm = new BindModel();

bm.addTable('second');

// bm._tables[0] === bm._tables['first'] == bm.first
// bm._tables[1] === bm._tables['second'] == bm.second
// bm._tables.count == 2
```
- Access to tables added as bm.second.
### \_Add as a collection of tables

You can add MetaTable through the \_tables.add() method, which adds tables to the \_tables collection similarly, but does not automatically generate a table name reference key, unlike addTable().

```js
var bm = new BindModel();

bm._tables.add('second');

// bm._tables[1] === bm._tables['second']
// bm._tables.count == 2
```


## Change the default table

If you set the added MetaTable to the default table, the default table is set to the changed table when you use addColumn() or addcommand() later.

```js
var bm = new BindModel();

bm._tables.add('second');

// Default Table Settings
bm._baseTable = bm['second'];  

// Add Column
bm.addColumnValue('aa', 10);

// bm.columns['aa'].value == 10
// bm._tables['first'].columns.count == 0
// bm._tables['second'].columns.count == 1
```
- For the 'column' property, refer to columns of '_baseTable' (column === \_tabeTable.column)

As such, MetaTable can be added and utilized in the BindModel object.


# Specify tables added by multiple methods

You can specify tables added by multiple methods. If you specify tables outside the \_tables collection, the object serialization feature is not available.

## BindModel Area

### addcommand(): add command

You can specify a table when adding a command.

```js
var bm = new BindModel();

bm.addTable('second');

bm.addCommand('read', 3, 'second');
bm.addCommand('list', 3, bm.second);

// bm.command['read']._baseTable == bm.second
// bm.command['list']._baseTable == bm.second
```
- The default table for the read command is specified as 'second'.
- The default table for the list command is specified as 'second'.
### addColumn(): add column

You can specify a table when adding columns.

```js
var bm = new BindModel();

bm.addTable('second');
bm.addCommand('read');

bm.addColumn('aa', 'read', 'valid', 'second');
bm.addColumn('bb', 'read', '$all', bm.second);
```
- The 'aa' column is registered in the 'second' table and a reference is registered in the valid of the read command.
- The 'bb' column is registered in the 'second' table and a reference is registered in the entire MetaView of the read command.

### addColumnValue(): add column

You can specify an initial value and set the table when adding columns.

```js
var bm = new BindModel();

bm.addTable('second');
bm.addCommand('read');

bm.addColumn('aa', 'AA', 'read', 'valid', 'second');
bm.addColumn('bb', 'BB', 'read', '$all', bm.second);
```
- The initial value of the 'aa' column is 'AA' and is registered in the 'second' table and a reference is registered in the valid of the read command.
- The initial value of the 'bb' column is 'BBB' and is registered in the 'second' table and a reference is registered in the entire MetaView of the read command.

### setMapping():column mapping

You can specify a default table when mapping

```js
var bm = new BindModel();
bm.addTable('second');
bm.addCommand('read');

bm.items.add('aa', '');
bm.items.add('bb', '');

bm.setMapping({
	aa: { read: ['valid'] },
	bb: { read: ['bind'] },
}, 'second');

// bm.second.columns.count == 2
// bm.first.columns.count == 0
```
- Items are registered in the 'second' table and the reference value is registered in the MetaView of the read command.

### Service Object

You can use it in combination with the table name in the items, mapping area.

```js
var bm1 = new BindModel({
	tables: ['second', 'three'],
	items: {
        'aa': '',
        'second.bb': '',
        'cc': '',
    },
    command: {
        read: {}
	},
    mapping: {
        'aa': { read: ['valid'] },
        'bb': { read: ['bind'] },
        'three.cc': { read: ['output'] }
    },
});
```
- Items 'aa' are registered in the default table (first) and mapped to the target command.
- The 'bbb' item is registered in the extra table (second) and mapped to the target command.
- The 'cc' item is registered in the additional table and mapped to the target command.


## Bind Command Area

### addColumn(): add column

You can specify a table when adding columns.

```js
var bm = new BindModel();

bm.addTable('second');
bm.addCommand('read');

bm.cmd['read'].addColumn('aa', 'valid', 'second');
bm.cmd['read'].addColumn('bb', '$all', bm.second);
```
- The 'aa' column is registered in the 'second' table and a reference is registered in the valid of the read command.
- The 'bb' column is registered in the 'second' table and a reference is registered in the entire MetaView of the read command.

### addColumnValue(): add column

When adding columns, you can set initial values and specify tables.

```js
var bm = new BindModel();

bm.addTable('second');
bm.addCommand('read');

bm.cmd['read'].addColumn('aa', 'AA', 'read', 'valid', 'second');
bm.cmd['read'].addColumn('bb', 'BB', 'read', '$all', bm.second);
```
- The 'aa' column is registered in the 'second' table with an initial value of 'AA' and a reference is registered in the valid of the read command.
- The 'bb' column is registered in the 'second' table with an initial value of 'AA' and a reference is registered in the entire MetaView of the read command.
### setColumn(): column settings

You can specify a table when setting the column.

```js
var bm = new BindModel();

bm.addTable('second');
bm.addCommand('read');

bb.first.columns.add('aa');
bb.first.columns.add('bb');
bb.seoncd.columns.add('cc');

bm.command['read'].setColumn('aa', 'valid');
bm.command['read'].setColumn(['bb', 'second.cc'], 'bind');
```
- The 'aa' column registered in the default table registers a reference in the valid of the read command.
- The 'bbb' column registered in the default table is referenced in the bind of the read command.
- The 'cc' column registered in the 'second' table is referenced in the bind of the read command.
