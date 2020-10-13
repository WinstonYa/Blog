---
title: 微信小程序弹出输入框的实现
date: 2020-06-23 15:08:23
categories: [微信小程序]
tags: 微信小程序
---

## 指定 modal 弹出框

​		在微信小程序的开发过程中，需要用到弹出输入框组件，但是在目前微信小程序当中并没有可以直接引用的弹出输入框的组件，在网上找了很多资料，接下来写一个可以应用的组件。

**常用属性**

| 属性名       | 类型        | 默认值 | 说明                       |
| ------------ | ----------- | ------ | -------------------------- |
| title        | String      |        | 标题                       |
| hidden       | Boolean     | false  | 是否隐藏整个弹框           |
| no-cancel    | Boolean     | false  | 是否隐藏 cancel 按钮       |
| confirm-text | String      | 确定   | confirm 按钮文字           |
| cancel-text  | String      | 取消   | cancel 按钮文字            |
| bindconfirm  | EventHandle |        | 点击确认触发的回调         |
| bindcancel   | EventHandle |        | 点击取消以及蒙层触发的回调 |

**示例代码**

```js
<!-- show.html-->
<!-- 创建团队按钮 -->
  <button
    class="create-team"
    hover-class="btn_hover"
    type="primary"
    hidden="{{hiddenName}}"
    bindtap="modalInput">
    创建团队
  </button>
  <modal
    hidden="{{hiddenmodalput}}"
    title="输入团队名称"
    confirm-text="确认"
    cancel-text="取消"
    bindcancel="cancel"
    bindconfirm="confirm">
    <input type='text' placeholder="请输入内容" auto-focus />
  </modal>
```

**js 的写法**

```js
Page({
  data: {
    //通过hidden属性控制弹框的显示和隐藏
    hiddenmodalput: true,
  },
  modalInput() {
    this.setData({ hiddenmodalput: !hiddenmodalput });
  },
  //取消按钮
  cancel() {
    this.setData({
      hiddenmodalput: true,
    });
  },
  //确认按钮
  confirm() {
    this.setData({
      hiddenmodalput: true,
    });
  },
});
```

**绑定 input 事件**

```js
<input
  type="text"
  placeholder="请输入内容"
  auto-focus
  name="inpval"
  bindinput="jsInput"
/>;
Page({
  data: {
    hiddenmodalput: true,
    //可以通过hidden是否掩藏弹出框的属性，来指定那个弹出框
    inpval: "",
  },
  //获取用户输入的input的值
  jsInput: function (e) {
    this.setData({ inpval: e.detail.value });
  },
  //确认
  confirm: function () {
    console.log(this.data.inpval);
  },
});
```
