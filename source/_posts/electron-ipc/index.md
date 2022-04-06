---
title: electron（send、sendSync、invoke）主进程与渲染进程通信的区别
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - 笔记
date: 2022-04-06 17:37:17
comments: true
tags: 
    - electron
    - 通信
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/loader/orange.progress-bar-stripe-loader.svg
---

## ipcRender.send

渲染进程 render.js

```js
// render.js
const { ipcRenderer } = require('electron');

// send 方法发送，并绑定另一个事件接收返回值
function sendMessageToMain() {
  ipcRenderer.send('render-send-to-main', '我是渲染进程通过 send 发送的消息');
}
ipcRenderer.on('main-reply-to-render', (event, message) => {
  console.log('replyMessage', message); // 'replyMessage 主进程通过 reply 回复给渲染进程的消息'
})
```

主进程 main.js：

```js
// main.js
const { ipcMain } = require('electron');

ipcMain.on('render-send-to-main', (event, message) => {
  console.log(`receive message from render: ${message}`)
  event.reply('main-reply-to-render', '主进程通过 reply 回复给渲染进程的消息')
})
```

1、主进程通过 ipcMain.on 来监听渲染进程的消息
2、主进程接收到消息后，可以回复消息，也可以不回复。如果回复的话，通过 event.reply 发送另一个事件，渲染进程监听这个事件得到回复结果。如果不回复消息的话，渲染进程将接着执行 ipcRenderer.send 之后的代码

## ipcRender.sendSync

渲染进程 render.js：

```js
// render.js
const { ipcRenderer } = require('electron');

function sendSyncMessageToMain() {
  const replyMessage = ipcRenderer.sendSync('render-send-sync-to-main', '我是渲染进程通过 syncSend 发送给主进程的消息');
  console.log('replyMessage', replyMessage); // '主进程回复的消息'
}
```

主进程 main.js：

```js
// main.js
const { ipcMain } = require('electron');

ipcMain.on('render-send-sync-to-main', (event, message) => {
  console.log(`receive message from render: ${message}`)
  event.returnValue = '主进程回复的消息';
})
```

1、主进程通过 ipcMain.on 来处理渲染进程发送的消息
2、主进程通过 event.returnValue 回复渲染进程消息
3、如果 event.returnValue 不为 undefined 的话，渲染进程会等待 sendSync 的返回值才执行后面的代码
4、请保证 event.returnValue是有值的，否则会造成非预期的影响

## ipcRender.invoke

渲染进程 render.js：

```js
// render.js
const { ipcRenderer } = require('electron');

async function invokeMessageToMain() {
  const replyMessage = await ipcRenderer.invoke('render-invoke-to-main', '我是渲染进程通过 invoke 发送的消息');
  console.log('replyMessage', replyMessage);
}
```

主进程 main.js：

```js
// main.js
const { ipcMain } = require('electron');

ipcMain.handle('render-invoke-to-main', async (event, message) => {
  console.log(`receive message from render: ${message}`)
  const result = await asyncWork();
  return result;
})

const asyncWork = async () => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('延迟 2 秒获取到主进程的返回结果')
    }, 2000)
  })
}
```

1、主进程通过 ipcMain.handle 来处理渲染进程发送的消息
2、主进程接收到消息后，可以回复消息，也可以不回复。如果回复消息的话，可以通过 return 给渲染进程回复消息；如果不回复消息的话，渲染进程将接着执行 ipcRenderer.invoke 之后的代码
3、渲染进程异步等待主进程的回应， invoke 的返回值是一个 Promise
