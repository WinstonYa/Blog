---
title: html/css
date: 2020-05-21 18:50:45
categories: [前端面试题,html/css]
tags: 前端面试
---

## 1. flex: 0 1 auto 表示什么意思

​		flex: 0 1 auto 其实就是弹性盒子的默认值，表示 `flex-grow`, `flex-shrink` 和 `flex-basis` 的简写，分别表示放大比例、缩小比例、分配多余空间之前占据的主轴空间.

​		1.flex-grow属性定义项目的放大比例，默认为零，即如果存在剩余空间，也不放大。

​		2.flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。

​		3.flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间，如果设置了width，则跟随width，如果没有设置width，则为内容实际宽度。

