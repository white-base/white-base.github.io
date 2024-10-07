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

last_modified_at: 2024-10-01
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


## Packaging

BindModel relies on axios and jQuery modules to perform asynchronous communication and DOM operations with the server; reflecting this dependency, it provides a variety of deployment packages.

### bindmodel.js

This package contains only BindModel and does not include axios and jQuery. This package is useful when externally already including axios and jQuery, or if you are managing them separately.

### bindmodel.pack.js

This package contains the axios and jQuery libraries along with BindModel. This package can be fully functional with just one bind-model.pack.js, without having to install axios or jQuery from the outside. 

{% capture notice-text %}
'Packy name + min.js' is a compressed file.
* bindmodel.min.js
* bindmodel.pack.min.js
{% endcapture %}

<div class="notice--info">
  See <h4>:</h4>
  {{ notice-text | markdownify }}
</div>