---
title: 简单实现Node的Events模块
date: 2020-07-02
categories: [前端面试题,面试题汇总]
tags: 前端面试
---



```javascript
const EventEmitter = require('events');

class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();
myEmitter.on('event', () => {
 console.log('an event occurred');
});
myEmitter.emit('event');
```

