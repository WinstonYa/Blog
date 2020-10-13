---
title: Vue-lifecycle
date: 2020-07-09 19:59:45
categories: [前端面试题,Vue]
tags: 前端面试
---

# Vue生命周期

**vue生命周期：从Vue实例创建、运行、到销毁期间，总是伴随着各种各样的事件，这些事件，统称为生命周期！**



![](Vue-lifecycle/vue_lifecycle.png)

beforeCreate // 调用该生命周期前已初始化生命周期，事件和渲染函数，不能访问到props等属性

created // 调用该生命周期前已顺序初始化具体的数据—— injections => props => methods => data => computed => watch => initProvide

beforeMount // 调用该生命周期前已初始化渲染函数$options.render

mounted // 调用该生命周期前已渲染真实节点

beforeUpdate // 状态改变时，会在nextTick中更新视图前调用

updated // 已调用render函数重新渲染

activated // keep-alive缓存组件渲染时调用(mounted前会渲染一次，每次updated前也会渲染一次)

deactivated // keep-alive缓存组件销毁后调用

beforeDestroy

destroyed

errorCaptured