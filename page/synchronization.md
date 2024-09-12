---
title: "Synchronization"
layout: single
permalink: /docs/synchronization/
date: 2011-06-23T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"
---

The execute() method of the Bindcommand object returns Promise, so you can use the async and wait keywords to execute commands asynchronously and write synchronization codes if necessary. 

Type: execute()
```ts
type execute () => Promise<void>;
```

## Example of synchronizing single-flawed fish

The following code executes a command called 'read_member' and displays a notification after that command is complete.
```js
var bm = new BindModel();

bm.addCommand('read_member', 3);
// ... omit column settings

async function readView() {
    try {
        await bm.cmd['read_member'].execute();
        alert( 'Called membership information');
    } catch (error) {
        console.error ('Error encountered during command execution:', error);
    }
}
```
- In this code, the execute() method returns Promise, so use the wait keyword to wait for the command to complete. If the command completes successfully, a notification message is displayed. You can also use the try...catch block to handle errors that may occur during the execution of the command.


## Multiple Command Synchronization Example

The following code executes two commands in sequence: read_meb and read_corp.
```js
var bm = new BindModel();

bm.addCommand('read_meb', 3);
bm.addCommand('read_corp', 3);
// ... omit column settings

async function readView() {
    try {
        await bm.cmd['read_meb'].execute();
        console.log ('calling private membership information');
        
        await bm.cmd['read_corp'].execute();
        console.log ('could get corporation information');
    } catch (error) {
        console.error ('Error encountered during command execution:', error);
    }
}
```
- The code executes two commands in sequence, and outputs a log message each time they complete. Use the try...catch block to handle errors that may occur during the execution of both commands.

## Example of a Simultaneous Run

Promise.all is available if you need to run commands simultaneously instead of sequentially.

```js
var bm = new BindModel();

bm.addCommand('read_meb', 3);
bm.addCommand('read_corp', 3);
// ... Column Settings
async function readView() {
    try {
        await Promise.all([
            bm.cmd['read_meb'].execute(),
            bm.cmd['read_corp'].execute()
        ]);
        console.log ('Called all information');
    } catch (error) {
        console.error ('Error encountered during command execution:', error);
    }
}
```
- In this code, use Promise.all to run both commands at the same time, and wait for all commands to complete. Once all commands have completed successfully, a log message will be output. If an error occurs, the catch block will handle it.

This makes it easy to synchronize promise-based asynchronous code using the async and wait keywords, allowing you to control the order of command execution and handle errors.
