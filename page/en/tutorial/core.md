---
lang: en
title: "Core concept"
layout: single
permalink: /docs/core/
date: 2024-08-14T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"

last_modified_at: 2024-10-01
# breadcrumbs: true
---

The core of BindModel is a combination of MetaTable and MetaView, which are important components of BindModel's functionality and are designed to minimize duplication and accommodate different cases in real-world projects.


## MetaTable Class

The MetaTable class easily represents data in a table format and provides a variety of features. The class offers features similar to DataTable in .NET, which is familiar to .NET experiences. MetaTable has two main attributes (columns and rows) that manage columns and rows to efficiently process data.

[55. MetaTable Class-B| - Reference: MetaTable Class]

### MetaTable.column property (collection)

The 'column' collection of 'MetaTable' provides additional/removal of columns.
This collection defines the table structure, and each column has its own key (column name) and index. This allows easy access to the column.

```js
var table1 = new MetaTabe('t1');

table1.columns.add('age');
table1.columns.add('gender');

// Access to a column
// table1.columns['age'] === table1.columns[0]
// table1.columns['gender'] === table1.columns[1]
// table1.colmmns.count === 2
```

#### Constraints
You cannot add or remove columns from the columns collection after MetaRow has been added to the rows collection in MetaTable. This is a constraint to maintain data consistency. The column definition must be completed before data can be inserted.

### MetaTable.rows Properties (Collection)

Therows property of MetaTable is a collection that allows you to add or remove data (MetaRow). New rows are created through the newRow() method, and the created MetaRow objects can be added to therows collection.

```js
var table1 = new MetaTabe('t1');
var row = table1.newRow();

row['age'] = 20;
row['gender'] = 'man';

table1.rows.add(row);

// Access to data
// row.count == 2
// table1.rows[0]['age'] == 20
// table1.rows[0]['gender'] == 'man'
// table1.rows.count == 1
```
- MetaRow, added to the rows collection, provides a continuous index.

## MetaView Class

The MetaView class performs a similar role to MetaTable, but an important difference is that, instead of adding columns directly to MetaView's columns collection, you can add them through references. This allows MetaView to define and leverage different views based on existing data structures.

[56. MetaView Class-B|Reference | -Reference: MetaView Class]

### Register a full column as a reference column

Once you have set the \_baseEntity property for MetaView, the column is actually added to \_baseEntity and only the reference value is registered for MetaView, which allows you to define and use separate views while still taking advantage of the structure of the existing table.

```js
var table1 = new MetaTabe('t1');
var view1 = new MetaView('v1', table1);

view1.columns.add('age');
view1.columns.add('gender');

// table1.colmmns.count === 2
// view1.colmmns.count === 2
// view1.columns['age'] === table1.columns['age']
// view1.columns['gender'] === table1.columns['gender']
```
- If a collection of existing column names exists when adding columns, register the reference value.

### Register an individual column as a reference column

MetaView can register only certain columns as reference values in \_baseEntity, which allows MetaView to selectively reference and use only certain data that is required.

```js
var table1 = new MetaTabe('t1');
var view1 = new MetaView('v1');

view1.columns.add('age');
view1.columns.add('gender', table1);

// table1.colmmns.count === 1
// view1.colmmns.count === 2
// view1.columns['gender'] === table1.columns['gender']
```
- If the target '\_baseEntity' is 'MetaView', it follows the cycle rule.


## HTML Column Classes

HTMLColumn is a child class of MetaColumn that provides the ability to associate HTML elements with data. In particular, the value attribute allows you to manage the current value of the column, and various properties allow you to flexibly control the setting and query of values. HTMLColumn's main properties include selector, setter/getter, and setFilter/getFilter.

[54. HTML Column Class-B| - Reference: Column Configuration]

### Set column value in MetaRow

You can use the setValue() method to import data from the MetaRow object and set it to the value in the column.

```js
var table1 = new MetaTabe('t1');
var row = table1.newRow();

row['age'] = 30;
row['gender'] = 'man';

tables.setValue(row);

// table1.columns['age'].value == 30
// table1.columns['gender'].value == 'man'
```

### Import value of column to MetaRow

You can use the getValue() method to return the value value of the column to the MetaRow type.

```js
var table1 = new MetaTabe('t1');

table1.columns['age'].value = 40;
table1.columns['gender'].value = 'man';

var row = tables.getValue();

// row['age'].value == 40
// row['gender'].value == 'man'
```

### Inquire the value of a column

When querying HTML Column's value value, one value is selected and returned according to the set priority.

#### Priorities
1. *getter return value*: If getter is set, this value is returned to the highest priority.
2. *getFilter return value*: If getFilter is set, the selector value is passed to the parameter and the processed value is returned.
3. *selector lookup value*: The value is returned depending on the type set in the selector (text, value, attr, prop, html).
4. *Internal value*: If there are no getter, getFilter, or selector settings, the values stored inside will be returned.
5. *default value*: If none of the above settings are present and the initial value is Empty, the default is returned. 
   (Initial value is null or empty string)
   
[54. HTML Column Class-B#getter Internal Structure | - Reference: getter Internal Structure]

Example: When setting getter
```js
column.getter = function(sVal) {
	return 'user value';
};

// column.value == 'user value'
```

Example: When setting up getFilter
```js
column.getFilter = function(sVal) {
	return $('input[name=type_cd]:checked').val(); // Blue
};

// column.value == 'Blue'
```

Example: When setting up getFilter and selector
```js
column.selector = { key: '#u_name', type: 'value' }; // Cat
column.getFilter = function(sVal) {
	return $('input[name=type_cd]:checked').val() +'-'+ sVal; // Blue
};

// column.value == 'Blue-Cat'
```
- The value of the selector is transmitted to the getFilter, and it is transmitted after processing in the getFilter.

Example: When setting up selector
```js
column.selector = { key: '#u_name', type: 'value' };
```

#### Refer to
getter and getFilter play similar roles, but there is a difference. getter is used to return a single set, and getFilter is responsible for setting or processing values for several HTML elements based on internal values (original values).

### Set value for column

When setting the value of HTML Column, save and pass the value in the order set. The setup procedure is as follows.

Setting Order
1. *setter*: The value you want to set is saved first.
2. *Internal*: If the setter has a return value, save the set value, and if there is no return value, store the set value internally.
3. *setFilter*: Set the value by passing the internal value as a parameter.
5. *selector*: Set the setFilter return value if there is no return value, and set the internal value if there is no return value.

[54. HTML Column Class-B#setter Internal Structure | - Reference: setter Internal Structure]

Example: When setting only the setter
```js
var temp;

column.setter = function(val) {
	temp = val;
};

column.value = 'user value';
// temp == 'user value'
// column.$value == 'user value'
```

Example: Set up setter and setFilter
```js
var temp;

column.setter = function(val) {
	temp = val * 10;
};
column.setFilter = function(val) {
	$('input[name=active_yn][value='+ val + ']').prop('checked', true);
};

column.value = 2;
// temp == 20
// <input type="checkbox" name="action_yn" value="2"/>
// column.$value == 2
```
- The parameter in setFilter is setter carries a return value, and if there is no return value, it carries an internal value.

Example: When setting the setter and selector 
```js
column.setter = function(val) {
	return temp = val * 10;
};
column.selector = { key: '#u_name', type: 'value' }; // Cat

column.value = 2;
// temp == 20
// <input type="text" name="u_name" value="20"/>
// column.$value == 20
```
- The setter return value is stored in the internal value as the return value.
- The selector's value is set to an internal value.

Example: When setting selector only
```js
column.selector = { key: '#u_name', type: 'value' };

column.value = 2;
// <input type="text" name="u_name" value="2"/>
// column.$value == 2
```

#### Refer to
setter and setFilter play similar roles, but there is a difference. setter is used to set a single value, and setFilter is responsible for setting or processing the values of several HTML elements based on the internal value (original value).
For example, when expressing a unit of currency, if the internal value is 1000, the screen outputs '1000'.


## Understanding the BindModel Structure

BindModel automatically creates one MetaTable (name first), and each Bindcommand object added within this structure contains three MetaViews (valid, bind, and output).

[[52. BindModel class-B#Main structure | - Reference: BindModel's main structure]
[53. Bind Command Class-B#Main Structure | - Reference: BindComand Main Structure]

### Summary of BindModel's Entities' Structures

Here is the code that summarizes BindModel's internal entity structure (MetaTable, MetaView):

```js
function createCommand(baseTable) {
	return {
		valid: new MetaView('valid', baseTable),
		bind: new MetaView('bind', baseTable),
		output: new MetaView('output', baseTable)
	};
}

var table1 = new MetaTabe('first');
var cmd1 = createCommand(table1);

cmd1.bind.columns.add('aa');
cmd1.output.columns.add('bb');

// table1.colmmns.count === 2 ('aa','bb')
// table1.columns['aa'] === view2.columns['aa']
// table1.columns['bb'] === view3.columns['bb']

// view1.colmmns.count === 0
// view2.colmmns.count === 1 ('aa')
// view3.colmmns.count === 1 ('bb')
```
- When you add a column to a MetaView, it is added to the MetaTable specified in the MetaView as\_baseEntity, where only the reference to the column is registered.
- When you add a column directly to the MetaTable, it is added independently; unlike MetaView, it is not referenced by any other view.
- When you create a BindModel, by default, a MetaTable named first is automatically created. Each Bind Command object automatically contains a MetaView named valid, bind, and output.

When you run the execute() method in BindModel, the column and data are set according to the structure of MetaTable and MetaView, which can be customized to meet a variety of user requirements.
