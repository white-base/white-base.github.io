---
lang: en
title: "Start"
layout: single
permalink: /docs/quick-start/
date: 2024-08-14T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"

last_modified_at: 2024-10-10
# breadcrumbs: true
---

## What is BindModel?

BindModel is a front-end framework that operates on the web and in Node.js environments. It is designed for simplicity and productivity based on commands and entities (Table, View). Once you are familiar with the basics of HTML, CSS, and JavaScript, you can easily create websites using BindModel.

- Manage all data as an entity (MetaTable, MetaView).
- It acts as a controller in the MVC pattern and is completely separate from the View.
- It provides a command-based processor to provide a consistent development pattern.
- It is a harmonious collection of libraries needed for web development, such as routing, form management, and client-server communication.
- It can be used in conjunction with other frameworks.


<!-- **Note:** You won't ever assign this layout directly to a post or page. Instead all other layouts will build off of it by setting `layout: default` in their YAML Front Matter.
{: .notice} -->


<!-- ![image-left](/assets/images/image-alignment-150x150.jpg){: .align-left} The rest of this paragraph is filler for the sake of seeing the text wrap around the 150×150 image, which is **left aligned**. There should be plenty of room above, below, and to the right of the image. Just look at him there --- Hey guy! Way to rock that left side. I don't care what the right aligned image says, you look great. Don't let anyone else tell you differently. -->


## Installation

### Installation using npm

To install BindModel in a Node.js environment, use the following command.

```sh
npm install logic-bind-model
```

### Installing in a browser environment

In a browser environment, BindModel is available via CDN.

```html
<script src="https://unpkg.com/logic-bind-model/dist/bindmodel.pack.js"></script>
```



## Use

BindModel is the core object of the framework.

### Server Environment (node.js)

In the Node.js environment, you can use the BindModel through a require or import statement.

Example: Using with CommonJS
```js
const { BindModel } = require('logic-bind-model');

const bm = new BindModel();
```


Example: Using with ES6
```js
import { BindModel } from 'logic-bind-model';  

const bm = new BindModel();
```

### HTML Environment

In the browser environment, it is accessed through the '_L' global variable. 

Example: Using in HTML Environments
```html    
<script src="https://unpkg.com/logic-bind-model/dist/bindmodel.pack.js"></script>
<script>
    const bm = new _L.BindModel();
</script>
```

## Student Information Inquiry Example

This is an example of code that implements the ability to retrieve student information from a server and display it on the screen from a web application. It uses the BindModel object to receive data through an AJAX request and binds it to an HTML element, allowing the user to verify the student's student number and name.

### Data Area

This area describes JSON data returned from the server.

Server Information: /user/1
```json
{
    "rows": {
        "user_no": "2020-1234",
        "user_name": "Mike"
    }
}
```

## Screen Area

The screen consists of two HTML elements that display the student's student number and name.

```html
<div>
    School number: <h2 id="user_no"></h2>
</div>
<div>
    이름 : <input id="user_name" type="text"/>
</div>
```

### Script Area

Scripts use 'BindModel' objects to retrieve data from servers and bind it to HTML elements.


```js
// step 1 
var bm = new BindModel();
bm.url = '/user/1';
// step 2
bm.addCommand('read', 3);
// step 3
bm.addColumn('user_no', 'read', 'output');
bm.addColumn('user_name', 'read', 'output');
// step 4
bm.columns['user_no'].selector = { key: '#user_no', type: 'text' };
bm.columns['user_name'].selector = { key: '#user_name', type: 'value' };
// step 5
bm.command['read'].execute();
```
1. Creates a 'BindModel' object.
   Set the bm.url property to /user/1 to specify the endpoint of the server.
2. Add a Bindcommand object named 'read'.
   The second factor represents the output option, and 3 sets the data to the value of the column from which it is received.
3. Add user_no and user_name columns.
   These columns serve as output for the 'read' command to receive data from the server.
4. Set the selector object for each column.
   Selector is a setting for binding HTML elements and data.
5. Run the 'read' command.
   Imported data is automatically reflected in HTML elements connected to selectors bound to each column. Data from user_no and user_name columns are bound to elements '#user_no' and '#user_name', respectively, and displayed on the screen.

This code shows the process of receiving student information from the server through the BindModel object and automatically binding it to the HTML element to display it to the user. Asynchronous communication and data binding with the server can be implemented with simple settings, which can be useful for web application development.


## Packaging

BindModel relies on axios and jQuery modules to perform asynchronous communication and DOM operations with the server; reflecting this dependency, it provides a variety of deployment packages.

### BindModel Pack

This package contains the axios and jQuery libraries along with BindModel. This package can be fully functional with just one bind-model.pack.js, without having to install axios or jQuery from the outside.

**ProTip:** <br/>- https://unpkg.com/logic-bind-model/dist/**bindmodel.pack.js**<br/>- https://unpkg.com/logic-bind-model/dist/**bindmodel.pack.min.js**
{: .notice--info}

### BindModel Core

This package contains only BindModel and does not include axios and jQuery. This package is useful when externally already including axios and jQuery, or if you are managing them separately.

**ProTip:** 
<br/>- https://unpkg.com/logic-bind-model/dist/**bindmodel.js**<br/>- https://unpkg.com/logic-bind-model/dist/**bindmodel.min.js**
{: .notice--info}