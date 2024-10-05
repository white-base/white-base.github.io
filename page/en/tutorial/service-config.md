---
lang: en
title: "Configure Service objects"
layout: single
permalink: /en/docs/service-config/
date: 2024-10-01T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"

# breadcrumbs: true
---

Service objects make it easy to create 'BindModel' objects, and inheritance can separate common areas for increased reuse.

The 'BindModel' object can be accessed by the this.bindModel property in the callback function.

Type: ServiceType
```ts
// items area
type KeyType = 'none' | 'value' | 'text' | 'html' | 'prop' | 'attr' | 'css';
type SelectorType = { key: string, type: KeyType };
type RegExpType = { reg: RegExp, msg: string, return?: boolean };
type FuncType = (value: any) => boolean;
type ConstraintType = RegExpType | FuncType;
type ColumnType = {
	selector?: SelectorType,
	getter?: () => any,
	setter?: (val: any) => any,
	getFilter?: () => any,
	setFilter?: (val; any) => any,
	default?: stirng | number | boolean | null,
	value?: any,
	alias?: string,
	caption?: string,
	constraints?: ConstraintType[] | ConstraintType,
	required?: boolean | false,
	columnName?: string
};
type Itemtype = {
	[key: string]: string | number | boolean | ColumnType
};
// command area
type CmdValueType = {
	outputOption?: 0 | 1 | 2 | 3,  // alias : outOpt
	config?: see axiosConfig, // axios type
	url?: string,
	views?: string[],
	cbBegin?: (cmd: BindCommand) => void,
	cbValid?: (view: MetaView, cmd: BindCommand) => boolean,
	cbBind?: (view: MetaView, cmd: BindCommand, cfg: object) => void,
	cbResult?: (data: object, cmd: BindCommand, res: object) => object,
	cbOutput?: (views: MetaViewCollection, cmd: BindCommand, res: object) => void,
	cbEnd?: (status: number, cmd: BindCommand, res: object) => void,
	onExecute?: (bindModel, bindCommand) => void,
	onExecuted?: (bindModel, bindCommand) => void,
};
type CommandType = {
	[key: string]: CmdValueType
};
// mapping area
typeColumnName = string; // 'item name' | 'column name' | 'table name.column name';
type CommandName = '$all' | string;  // string = 'command name'
type ViewName = 'valid' | 'bind' | 'output' | '$all' | string; // add view name
type MappingType = {
	[key: ColumnName]: {
		[key: CommandName]: ViewName | ViewName[]
	}
};
// fn area
type fnType = {
	[key: string]: Function
};
// -------------------------
// service area
type ServiceType = {
	tables?: string | string[],
	baseConfig?: axiosConfig,  // // axios type
	url?: string,
	cbBaseBegin?: (cmd: BindCommand) => void;
	cbBaseValid?: (view: MetaView, cmd: BindCommand) => boolean,
	cbBaseBind?: (view: MetaView, cmd: BindCommand, cfg) => void,
	cbBaseResult?: (data: object, cmd: BindCommand, res) => object,
	cbBaseOutput?: (views: MetaViewCollection, cmd: BindCommand, res: object) => void,
	cbBaseEnd?: (status: number, cmd: BindCommand, res: object) => void,
	onExecute?: (bindModel, bindCommand) => void,
	onExecuted?: (bindModel, bindCommand) => void,
	items?: Itemtype,
	command?: CommandType,
	mapping?: MappingType,
	fn?: fnType,
	preRegister?: (bindModel) => void',
	preCheck?: (bindModel) => boolean',
	preReady?: (bindModel) => void,

};
```

# Service Object

## Configuring the Default Area

Configures the default callback function and server request information for the service object. 

Type: Default server request and default callback function
```ts
// Configuring Callback and Server Requests on Service Objects
type ServiceType = {
	baseConfig?: axiosConfig,  // axios 타입 참조
	url?: string,
	cbBaseBegin?: (cmd: BindCommand) => void;
	cbBaseValid?: (view: MetaView, cmd: BindCommand) => boolean,
	cbBaseBind?: (view: MetaView, cmd: BindCommand, cfg) => void,
	cbBaseResult?: (data: object, cmd: BindCommand, res) => object,
	cbBaseOutput?: (views: MetaViewCollection, cmd: BindCommand, res: object) => void,
	cbBaseEnd?: (status: number, cmd: BindCommand, res: object) => void,
	onExecute?: (bindModel, bindCommand) => void,
	onExecuted?: (bindModel, bindCommand) => void,
};
```
- 'url' is the value of 'baseConfig.url'.

Example: Basic
```js
var bm = new BindModel({
	// Default Server Request
	baseConfig: { method: 'GET' },
	url: '/user',
	// Default callback function
	cbBaseBegin: function(cmd) { 
		console.log ('default start callback); 
	},
	cbBaseValid: function(view, cmd) {
		console.log ('default validation callback);
		return true;
	},
	cbBaseBind: function(view, cmd, cfg) { 
		console.log ('Basic Server Request Callback'); 
	},
	cbBaseResult: function(data, cmd, res) {
		console.log ('Default Server Response Callback');
		return data;
	},
	cbBaseOutput: function(vidw, cmd, res) => { 
		console.log ('Default Response Output Callback'); 
	},
	cbBaseEnd: function(status, cmd, res) => { 
		console.log ('Default Termination Callback'); 
	},	
});
```

Example: Basic Configuration by Method
```js
var bm = new BindModel();

// Default Server Settings
bm.baseConfig = { method: 'GET', url: '/user' };
bm.cbBaseBegin = function(cmd) { 
	console.log ('default start callback); 
};

// Default callback function
bm.cbBaseValid: function(view, cmd) {
	console.log ('default validation callback);
	return true;
};
bm.cbBaseBind: function(view, cmd, cfg) { 
	console.log ('Basic Server Request Callback'); 
};
bm.cbBaseResult: function(data, cmd, res) {
	console.log ('Default Server Response Callback');
	return data;
};
bm.cbBaseOutput: function(vidw, cmd, res) => { 
	console.log ('Default Response Output Callback'); 
};
bm.cbBaseEnd: function(status, cmd, res) => { 
	console.log ('Default Termination Callback'); 
};
```
- Same as the service object configured above.

The default configuration can be set by command, and 'command' has a higher priority than the default configuration.

## Configuring a Table Area

Configures additional table information for service objects. 

Type: Tables
```ts
// Configuring Tables in a Service Object
type ServiceType = {
	tables?: string | string[],
};
```

The 'BindModel' object automatically generates and uses the 'MetaTable' named 'first'. Set the additional table to the 'tables' property.


```js
var bm = new BindModel({
    tables: ['second', 'third']
});

// Refer to the main table
// bm.first === bm._tables['first']  // true
// bm.first === bm._tables[0]        // true

// See Additional Table
// bm.second === bm._tables['second'] // true
// bm.second === bm._tables[1]        // true
// bm.third === bm._tables['third']   // true
// bm.third === bm._tables[2]         // true
```
- Use when you need an additional table.

Example: Configuring a Table by Method
```js
var bm = new BindModel();

// Create a table
bm.addTable('second');
bm.addTable('third');
```
- Same as the service object configured above.


## Configuring the Item Area

Configures the column raw value for the service object.

### Setting Raw Values for Columns

- items Each element has the raw value of the column. 
- If the key value of items is of type 'string, number, boolean, null', it is set to the value of the column. 
- If the key value of items is of the 'object' type, it is used as the property value of the column.

Type: items
```ts
type KeyType = 'none' | 'value' | 'text' | 'html' | 'prop' | 'attr' | 'css';
type SelectorType = { key: string, type: KeyType };
type RegExpType = {reg: RegExp, msg: string, return?: boolean = true};
type FuncType = (value: any) => boolean;
type ConstraintType = RegExpType | FuncType;
type ColumnType = {
	selector?: SelectorType,
	getter?: () => any,
	setter?: (val: any) => any,
	getFilter?: () => any,
	setFilter?: (val: any) => any,
	default?: stirng | number | boolean | null,
	value?: any,
	alias?: string,
	caption?: string,
	constraints?: ConstraintType[] | ConstraintType,
	required?: boolean | false,
	columnName?: string
};
type Itemtype = {
	[key: string]: string | number | boolean | ColumnType
};
// Configuring items in a service object
type ServiceType = {
	items?: ItemType
};
```

### item naming rule
- Column Name : The column is registered in the default table.
- Table name.Column name: The column is registered in the target table.
### items property description
- 'string, number, boolean, null ': The raw value is set to the value of the column.
- selector : Selector to use when importing or setting the value of a column from DOM.
- setter : A function to use when setting an external value.
- getter : A function to use when importing values from outside.
- required : Sets whether the column value is required or not.
- setFilter : A filter function to use when setting the value.
- getFilter —Filter function to use to import value.
- constructs —Set the constraints of the value with a regular expression or user function, used for validation.

[[54. HTML Column Class-B|Refer to HTML Column]]


Example: items
```js
var bm = new BindModel({
	// Create additional tables
	tables: 'second',
	
	// Create an item
	items: {
		aa: 'Cat',
		'second.bb': 10,
		'second.cc': true,
		dd: {
			selector: { key: '#U_ID', type: 'value' },  // 컬럼의 selector 설정
			setter: (val) => {/*Outside setting area*/}, // Setter for column
			getter: () => { return 'external value'; }, // set getter for column
		},
		ee: {
			required: true, // required setting of column
			setFilter: (val) => {/*Outside setting area*/}, // SetFilter on column
			getFilter: () => { return 'external value'; }, // set getFilter for column
		},
		ff: {
			constructs: {reg: /abc/, msg: 'Failed to match!' } // Set Constraints for Column
		}
	}
});
```
- The item aa' is registered as a column in the default table, and 'Cat' is set in the value.
- The item 'bb' registers as a column in the second table, and sets value to 10.
- The cc' item registers as a column in the second table and sets true to the value.

Example: Configuring an Item Using the Method
```js
var bm = new BindModel();

// Create additional tables
bm.addTable('second');

// Create an item
bm.items.add('aa', 'Cat');
bm.items.add('second.bb', 10);
bm.items.add('second.cc', true);
bm.items.add('dd', {
	selector: { key: '#U_ID', type: 'value' },
	setter: function(val) {/*external setting area*/},
	getter: function() { return '외부값'; }, 
});
bm.items.add('ee', {
	required: true,
	setFilter: function(val) {/*external setting*/},
	getFilter: function() { return '외부값'; },
});
bm.items.add('ff', {
	constructs: {reg: /abc/, msg: 'Failed to match!' }
});
```
- Same as the service object configured above.

Items can also help manage data when registering the same column in multiple tables.

## Configuring Command Areas

Configures command information for a service object.

Type: command
```ts
type CmdValueType = {
	outputOption?: 0 | 1 | 2 | 3,  // 별칭 : outOpt
	config?: see axiosConfig, // axios type
	url?: string,
	views?: string[],
	cbBegin?: (cmd: BindCommand) => void,
	cbValid?: (view: MetaView, cmd: BindCommand) => boolean,
	cbBind?: (view: MetaView, cmd: BindCommand, cfg: object) => void,
	cbResult?: (data: object, cmd: BindCommand, res: object) => object,
	cbOutput?: (views: MetaViewCollection, cmd: BindCommand, res: object) => void,
	cbEnd?: (status: number, cmd: BindCommand, res: object) => void,
	onExecute?: (bindModel, bindCommand) => void,
	onExecuted?: (bindModel, bindCommand) => void,
};
type CommandType = {
	[key: string]: CmdValueType
};
// Configuring a command in a service object
type ServiceType = {
	command?: CommandType,
};
```
- For 'CmdValueType.url', see the value 'CmdValueType.config.url'.
- 'CommandType' key is the name of the 'command' to be added.

### Command Type Description
-  outputOption —Specifies how the view is output; the default is 0. 
-  config : Same as the setting object of axios.
-  url : 'config.url' value, setting the URL path to request via axios.
- views —Name of the output view (MetaView) to be added.
- cbBegin : This is the callback function before the start.
- cbValid : Validation callback function.
- cbBind : This is the server request callback function.
- cbResult —The server response callback function.
- cbOutput —Answer output callback function.
- cbEnd —Callback function before termination.

[24. Bind Command Composition - C#Callback (Attribute)]
Example: command
```js
var bm = new BindModel({
	command: {
		create: {},
		read: {
			outputOption: 3, // Set data to column value (specified column)
			config: {method: 'GET' }, // GET request setting
			cbEnd: function() { alarm('Normal Processed') ; } // Callback function after processing is completed
		},
		update: {
			views: ['two'], // adding 'two' views
			url: '/user' // URL path to request
		}
	}
});
```
- I created "create" 
	- Set the default output option = 0.
- We created "read"
	- Set output option = 3
	- Set the config server request and
	- Sets the cbEnd callback function.
- Create 'update' and
	- Set the default output option = 0 and
	- I'll add "two" to the output view
	- Set the server request url.

Example: Configuring Commands by Method
```js
var bm = new BindModel();
// command generation
bm.addCommand('create');
bm.addCommand('read');
bm.addCommand('update');

// Set the read command
bm.commmand['read'].outputOption = 3;
bm.commmand['read'].config = { method: 'GET' };
bm.commmand['read'].cbEnd = { 
	alert('Normal Processed');
};

// update command setting
bm.commmand['update'].newOutput('two');
bm.commmand['update'].url = '/user';
```
- Same as the service object configured above.


## Configuring Mapping Areas

Configure the mapping of items and 'command' in the service object.

Type: Mapping
```ts
typeColumnName = string; // 'item name' | 'column name' | 'table name.column name';
type CommandName = '$all' | string;  // string = '명령 이름'
type ViewName = 'valid' | 'bind' | 'output' | '$all' | string; // 추가 뷰 이름
type MappingType = {
	[key: ColumnName]: {
		[key: CommandName]: ViewName | ViewName[]
	}
};
// Configuring Mapping on a Service Object
type ServiceType = {
	mapping?: MappingType
};
```

### Mapping Rules
- Column Name 
	- 'Column Name' : The column in the base table is selected.
	- ''Table name.Column name' : A column for the specified table is selected. (Create when none exists) #REVIEW
- Command Name 
	- ' 'command name' : The specified command is selected.
	- '$all' : Full command is selected.
- View Name (ViewName)
	- 'valid', 'bind', 'output' : Map to the selected MetaView (multiple is specified as array)
	- '$all' : Map to the entire MetaView (including added output)
	
[00. Column mapping]

Example: Mapping
```js
var bm = new BindModel();
bm.setService({
	// Table Area
	tables: ['second'],
	// Item Area
	items: {
		aa: '',
		bb: '',
		cc: '',
		dd: ''
	},
	// command area
	command: {
		one: {},
		two: {},
	},
	// Mapping area
	mapping: {
		aa: { $all: ['valid']}, // Register 'aa' in the 'valid' view of all commands
		bb: {one: ['bind']}, // Register 'bbb' in 'bind' view of 'one' command
		'second.cc ': {two: ['output'] } // Register 'cc' in 'output' view of 'two' command
	}
});

// bm.items.count == 4 ('aa','bb','cc','dd')
// bm.first.columns.count == 2 ('aa','bb')
// bm.second.columns.count == 1 ('cc')
// bm.cmd['one'].valid.columns.count == 1 ('aa')
// bm.cmd['one'].bind.columns.count == 1 ('bb')
// bm.cmd['one'].output.columns.count == 0
// bm.cmd['two'].valid.columns.count == 1 ('aa')
// bm.cmd['two'].bind.columns.count == 0
// bm.cmd['two'].output.columns.count  == 1 ('cc')
```
- Items 'aa' are registered in the default table and mapped to the valid (MetaVeiw) of all commnads.
- Items 'bbb' are registered in the default table and mapped to the bind (MetaVeiw) of the 'one' command.
- The 'cc' item is registered in the 'second' table and mapped to the output (MetaVeiw) of the 'two' command.

Type: setMapping()
```ts
type setMapping(
	mapping: PropertyCollection | object, 
	bTable?: MetaTable | string
) => void;
```
- mapping : The collection to be mapped.
- bTable : Default table to be mapped, default is \_baseTable.

Example: Configuring Mapping by Method
```js
var bm = new BindModel();

// Create additional tables
bm.addTable('second');

// Create an item
bm.items.add('aa', '');
bm.items.add('bb', '');
bm.items.add('cc', '');
bm.items.add('dd', '');

// command generation
bm.addCommand('one');
bm.addCommand('two');

// Item mapping
bm.setMapping({
	aa: { $all: ['valid']}, // Register 'aa' in the 'valid' view of all commands
	bb: {one: ['bind']}, // Register 'bbb' in 'bind' view of 'one' command
	'second.cc ': {two: ['output'] } // Register 'cc' in 'output' view of 'two' command
})
```
- Same as the service object configured above.

You can efficiently map the columns required for a specific view of each command, which helps to maintain consistency in data processing and improve management convenience.

## Configuring a Function Area

Configures the user function of the service object.

Type : fn
```ts
type fnType = {
	[key: string]: Function;
};
// Configuring fn in a service object
type ServiceType = {
	fn?: fnType
};
```
- The key is the user function name.

Example: fn
```js
var bm = new BindModel({
	cbBaseBegin: function(cmd) {
		Access the parameter at cmd._model.fn.ecCreate(); //cmd
		this.bindModel.fn.sum(1, 1); // this.bindModel로 접근
	},
	command: {
		create: {
			cbEnd: function() {
				this.bindModel.fn.sum(1, 2);
			}
		},
	},
	fn: {
		sum: function(a, b) {return a + b},
		execCreate: function() {
			this.bindModel.cmd.read.execute();
		}
	}
});

// Register for an event
$('#btn_create').click(function() {
	bm.fn.execCreate();
});

```
- You can access the BindModel object with the 'parameter' or 'this.bindModel' properties in the callback function.

Example: Configuring a Method as a Function
```js
var bm = new BindModel();

// Configuring Functions
bm.fn.add('sum', function(a, b) {return a + b});
bm.fn.add('execCreate', function() {
	this.bindModel.cmd.read.execute(); // this.bindModel 로 접근
});

// Common callback configuration
bm.cbBaseBegin = function(cmd) {
	cmd._model.fn.ecCreate(); // Access to the cmd parameter
	this.bindModel.fn.sum(1, 1); // this.bindModel 로 접근
};

// Configuring Commands
bm.addCommand('create');
bm.command['create'].cbEnd = function() {
	this.bindModel.fn.sum(1, 2);  // this.bindModel 로 접근
}

// Register for an event
$('#btn_create').click(function() {
	bm.fn.execCreate(); // Access functions from outside
});
```
- Same as the service object configured above.

Each area can increase the degree of engagement and increase reusability and maintenance.


## Configuring the Preprocessing Area

Configures preprocessing information for service objects, primarily used to automate service objects.

타입 : init(), preRegister, preCheck, preReady
```ts
type init = () => void;

type preRegister = (bindModel) => void; 
type preCheck = (bindModel) => boolean; 
type preReady = (bindModel) => void; 
```

### Preprocessing Call Flow

1. The init() mesodle calls are preRegister, preCheck, and preReady sequentially.
2. If you return false from preCheck, preReady is called cbFail without calling.
3. If you return true from preCheck, a preReady call is made.

Preprocessing is used for interaction between service objects and screen pages or selector validation.

Example: Pre-processing
```js
var bm = new BindModel({
	preRegister: function(bindModel) { 
		// Pre-processing: Before the inspection
	},
	preCheck: function(bindModel) {
		// Pre-processing: Inspection
		if (bm.checkSelector().length === 0) return true;
	},
	preReady: function(bindModel) { 
		// Pre-processing: Ready
		bindModel.command['test'].execute();
	},
});

$(document).ready(function () {
	bm.init();
});
```
- When the page is ready, call the init() method to inspect the DOM and run the 'test' command.

Example: Pre-processing a Method
```js
var bm = new BindModel();
// BindModel Settings...

bm.preRegister = function(bindModel) { 
	// Pre-processing: Before the inspection
};
bm.preCheck = function(bindModel) { 
	// Pre-processing: Inspection
	if (bm.checkSelector().length === 0) return true;
};
bm.preReady = function(bindModel) { 
	// Pre-processing: Ready
	bindModel.command['test'].execute();
};

$(document).ready(function () {
	bm.init();
});

```
- Same as the service object configured above.

Pre-processing areas can be utilized if automation is required.

# function

## To inject service objects

When creating a 'BindModel' object, you can either pass parameters or call the setService() method to inject service objects. 

Type: setservice()
```ts
type setService = (service: IService, isTypeCheck: boolean = false) => void;
```
-  service —The service object to be injected.
- isTypeCheck : Sets whether to perform a type check; the default is false.

As a setService() method, it separates service objects to enhance readability and maintenance of code.

Example: Injection via Creator
```js
var bm1 = new BindModel({
	items: {
        aa: 'Cat',
        bb: 10,
        cc: true,
    },
    fn: {
        sum: function(a, b) { return a + b; },
    },
    url: '/user',
    command: {
        read: {
            outputOption: 3,
            cbEnd: function() { console.log('Normal Processed'); }
        },
        update: {
            views: ['two'],
            url: '/user'
        }
    },
    mapping: {
        aa: { $all: ['valid'] },
        bb: { read: ['bind'], update: 'output' },
        cc: { update: ['output'] }
    },
});
```

Example: Inject with setService() Method
```js
// items, fn configuration
var svcItems = {
	items: {
        aa: 'Cat',
        bb: 10,
        cc: true,
    },
    fn: {
        sum: function(a, b) { return a + b; },
    }
};

// Other configurations
var svcCommon = {
    baseConfig: { method: 'GET' },
    url: '/user',
    command: {
        read: {
            outputOption: 3,
            config: { method: 'GET' },
            cbEnd: function() { console.log('Normal Processed'); }
        },
    },
    mapping: {
        aa: { $all: ['valid'] },
        bb: { read: ['bind'], update: 'output' },
        cc: { update: ['output'] }
    },
};

barbm = new BindModel(); // Injection by Parameters

bm2.setService(svcItems);
bm2.setService(svcCommon);
```
- Same as the service object configured above.
- In the first setService() method call, set items, fn to service.
- The second setService() method call sets up services such as command, mapping, and so on.

Duplicate settings retain the last-minute value, and are added for event values.

Areas 'items', 'fn' have low dependence on other areas.
Service objects allow you to manage common settings and increase reusability.

## Defining a service class

### Create a service object through inheritance

You can create a service in a class to increase the reuse of the common part of the code.
Service classes can be used in various structures.

common-svc.js
```js
class CommonService() {
	cbFail = function(msg) {
		console.warn ("user failure handling:+ msg");
	};
	cbError = function(msg) {
		console.error ("User error handling")
	};
}
```
- Common areas were created as common service classes.

member-svc.js
```js
class MemberService(suffix) extends CommonService {
	items = {
		idx: -1,
		user_no: { selector: { key: '#user_no'+ suffix, type: 'value' } },
		u_name: { selector: { key: '#u_name'+ suffix, type: 'value' } },
	};
	command = {
		create: 0,
		read: {
			outputOption: 3,
			cbEnd: () => { alert('Normal Processed'); }
		}
	};
	mapping = {
		idx: { 
			read: ['valid', 'bind'] 
		},
		u_name: { 
			create: ['valid', 'bind'],
			read: ['output']
		},
		user_no: { 
			create: ['bind'],
			read:: ['output'],
		}
	};
	preCheck = function(bindModel) { 
		if (bm.checkSelector().length === 0) return true;
	}
};
```
- The suffix parameter is a prefix for preventing conflicts in the selector name.

member.html
```html

<div>
	Class number <h2 id="user_no"></h2>
</div>
<div>
	이름 <input id="u_name" type="text"/>
</div>
<button id="btn_Create" type="button">추가</button>

<script src="BindModel.js"></script>
<script src="common-svc"></script>
<script src="member-svc"></script>
<script>

	var meb = new _L.BindModel(new MemberService());
	
	meb.url = 'http://SEVER_URL'; // Set the request path.
	meb.preReady = function(bindModel) {
		$('#btn_Create').click(bindModel.command.execute());
	};
	
	$(document).ready(function () {
		meb.init();
	});

</script>
```
- When the page is ready, the init() method is called to validate the selector in preReady.
- When you click the 'Add' button, run command.create.execute() to bind the server request result to the screen.

Easy to manage common parts such as paging processing on the screen.


