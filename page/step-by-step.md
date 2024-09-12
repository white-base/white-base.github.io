---
title: "Step-by-step process"
layout: single
permalink: /docs/step-by-step/
date: 2011-06-23T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"
---

It is an HTML production process that uses BindModel to process customer information registration, inquiry, and correction functions.

## Registering customer information

Since it is a screen that is used by sharing columns in several commands, it was created using the service object injection method.
### 1. Create membership HTML

```html
<div>
	name <input type="text" id="user_name"/>
</div>
<div>
	man<input type="radio" name="gender" value="male" />
	woman<input type="radio" name="gender" value="female" />	
</div>
<div>
	tel <input type="text" id="tel"/>
</div>

<button type="button" id="btn_create">등록</button>
```
- This HTML includes an entry field and a register button to enter your username, gender, and contact information.
### 2. Set Items and Commands

```js
var bm = new BindModel({
	items: {
		user_name: {
			selector: { key: '#user_name', type: 'value' },
			required: true
		},
		gender: {
			setFilter: function(val) { 
				$('input[name=gender][value='+ val + ']').prop('checked',true);
			},
			getFilter: function() { 
				return $('input[name=gender]:checked').val() 
			}
		},
		tel: {
			selector: { key: '#tel', type: 'value' },
			constraints: [
				{ regex: /\d{3}-\d{3,4}-\d{4}/, msg: "Not in phone number format."}
			]
		}
	},
	command: {
		create: {
			cbEnd: function() {
				alert( 'Registration processed successfully');
			}
		}
	},
	mapping: {
		user_name: { create: ['valid', 'bind'] },
		gender:    { create: ['bind'] },
		tel:       { create: ['bind', 'bind'] }
	}
});
bm.url = '/user';
```
- items define the input fields and their characteristics.
	- user_name : Binds with element with ID 'user_name' and is required.
	- gender : Use a filter to set and import radio buttons.
	- tel: contains a validation that binds to an element with ID 'tel' and checks the phone number format.
- command defines the interaction with the server.
	- create—Registration-related command, including a callback function that alerts you upon completion.
- Mapping defines the mapping between items and commands.
	- Sets how the user_name, gender, and tel fields are mapped to the create command.

### 3. Event registration

```js
$('#btn_create').click(() => bm.command['create'].execute());
```
- When the register button is clicked, run the create command.

## Looking up customer information

Example Server Data
```json
// Restful :/user/1
{
	"rows": [
		{
			"user_name": "Hong Gildong",
			"gender": "male",
			"tel": "010-123-1234"
		}
	]
}
```
- Data in JSON format that you will receive when you query the server.
### 1. Add html

```html
<input type="hidden" id="idx"/>
```
- Add a hidden field to store the ID of the customer you want to look up.

### 2. items, command, mapping 추가

```js
var bm = new BindModel({
	items: {
		idx: {
			selector: { key: '#idx', type: 'value' },
		}
	},
	command: {
		read: {
			outputOption: 3
		}
	},
	mapping: {
		idx:       { read: ['valid', 'bind'] },
		user_name: { read: ['output'] },
		gender:    { read: ['output'] },
		tel:       { read: ['output'] }
	}
});
```
-  Save the customer ID by adding the idx item to the items.
- Define the lookup function by adding the read command to the command.
- Mapping maps idx, user_name, gender, tel items to read commands.

### 3. Get idx from url and read it

```js


$(document).ready(function () {
	var idx = window.location.href.split('=')[1];
	if (idx) {
		$("#idx").val(idx);  // input hidden 설정
		bm.command['read'].execute();
	}
});
```
- When the page loads, take the idx value from the URL and set it in the idx column, run the read command to query the data.

## To modify customer information

### 1. Add a modification button

```html
<button type="button" id="btn_update">수정</button>
```
- Add the Modify button.

### 2. Add command and mapping

```js
var bm = new BindModel({
	command: {
		update: {
			cbEnd: function() {
				alert('Corrected processed');
			}
		}
	},
	mapping: {
		user_name: { update: ['valid', 'bind'] },
		gender:    { update: ['bind'] },
		tel:       { update: ['bind', 'bind'] }
	}
});
```
-  Define modifications by adding the update command to the command.
-  In mapping, map the user_name, gender, and tel columns with the update command.
### 3. Event registration

```js
$('#btn_update').click(() => bm.command['update'].execute());
```
- When the edit button is clicked, run the update command.


## Full Source (Inquiry, Registration, Modification)

### 1. Body area

```html
<input type="hidden" id="idx"/>
<div>
	name <input type="text" id="user_name"/>
</div>
<div>
	man<input type="radio" name="gender" value="male" />
	woman<input type="radio" name="gender" value="female" />	
</div>
<div>
	tel <input type="text" id="tel"/>
</div>

<button type="button" id="btn_create">create</button>
<button type="button" id="btn_update">update</button>
```

### 2. Script Area

```html
<script>
var bm = new BindModel({
	items: {
		idx: {
			selector: { key: '#idx', type: 'value' },
		},
		user_name: {
			selector: { key: '#user_name', type: 'value' },
			required: true
		},
		gender: {
			setFilter: function(val) { 
				$('input[name=gender][value='+ val + ']').prop('checked',true);
			},
			getFilter: function() { 
				return $('input[name=gender]:checked').val() 
			}
		},
		tel: {
			selector: { key: '#tel', type: 'value' },
			constraints: [
				{ regex: /\d{3}-\d{3,4}-\d{4}/, msg: "Not in phone number format."}
			]
		}
	},
	command: {
		create: {
			cbEnd: function() {
				alert( 'Registration processed successfully');
			}
		},
		read: {
			outputOption: 3
		},
		update: {
			cbEnd: function() {
				alert('Corrected processed');
			}
		}
	},
	mapping: {
		idx:       {
			read: ['valid', 'bind'] 
		},
		user_name: { 
			create: ['valid', 'bind'],
			update: ['valid', 'bind'],
			read:   ['output'] 
		},
		gender: { 
			create: ['bind'],
			update: ['bind'],
			read:   ['output']
		},
		tel: { 
			create: ['valid', 'bind'],
			update: ['valid', 'bind'],
			read:   ['output']
		}
	}
});
bm.url = '/user';

$(document).ready(function () {
	var idx = window.location.href.split('=')[1];  // page?idx=764998
	// Register for an event
	$('#btn_create').click(() => bm.command['create'].execute());
	$('#btn_update').click(() => bm.command['update'].execute());
	if (idx) {
		$("#idx").val(idx);  // input hidden setting
		bm.command['read'].execute();
	}
	
	// bm.columns.user_name.value == 'Neo'
	// bm.columns.gender.value == 'male'
	// bm.columns.tel.value == '010-123-1234'
});
</script>
```

This code is a simple and intuitive example of using BindModel to establish binding between input fields and commands and interactions with servers. It contains all the elements needed to implement the ability to register, query, and modify customer information, and efficiently handles user input and data exchange between servers.
