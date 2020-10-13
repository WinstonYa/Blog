---
title: specification
date: 2020-08-13 16:31:34
categories: [前端风格规范]
tags: 前端风格规范
---

# 前端风格规范

Author：@Winston Wong

Date：2020.06.12

Keywords：less、babel、router、vuex、eslint、unit-jest

## 一、框架的选择

[Vue.js 官方文档](https://cn.vuejs.org/)     [Vue CLI 官方文档](https://cli.vuejs.org/zh)

在官网的指引下，下载 @vue/cli 后，运行 vue create 项目名 来创建一个新项目，你会被提示选取一个 preset，若已有符合风格规范的设置，可直接选择，如：

![](specification/preset.png)

否则选“手动选择特性”（Manually select features）来选取需要的特性，步骤如下：

1. Check the features needed for your project：

   ​	Babel

   ​	Router

   ​	Vuex

   ​	CSS Pre-processors

   ​	Linter / Formatter

   ​	Unit Testing

1. Use history mode for router?           Y

1. Pick a CSS pre-processor：               Less

1. Pick a linter / formatter config：      ESLint + Prettier

1. Pick additional lint features：           Lint on save，Lint and fix on commit

1. Pick a unit testing solution：             Jest

过程图如下：

![](specification/preset-1.png)

![](specification/preset-2.png)

![](specification/preset-3.png)

![](specification/preset-4.png)

![](specification/preset-5.png)

![](specification/preset-6.png)

配置中的 ESLint 主要负责代码规则校验，Prettier 主要负责代码风格优化，它们主要依赖的插件如下：

- [Perttier](https://prettier.io/)：代码格式化工具

- [eslint(recommended)](https://eslint.org/docs/user-guide/getting-started)：eslint 中包含的推荐规则

- [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier)：eslint 中使用 prettier 进行检查

- [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)：用于解决 eslint 与 prettier 的规则冲突

## 二、配置 commitlint

1、Vue CLI 手脚架生成后，配置 commitlint，下载依赖包：

```
yarn add -D @commitlint/{cli,config-conventional}
```

- [@commitlint/cli](https://commitlint.js.org/)：脚手架基础设施

- [@commitlint/config-conventional](https://github.com/conventional-changelog/commitlint)：官方推荐规则

2、根目录下新建 commitlint.config.js 文件：

```js
// commitlint.config.js
*module*.*exports* = {
 extends: ["@commitlint/config-conventional"]
};
```

3、修改 package.json，添加 git commit 钩子：

```js
// package.json
{
...
 *"gitHooks"*: {
  *"pre-commit"*: "lint-staged",
  *"commit-msg"*: "commitlint -E GIT_PARAMS"     // 添加 git commit 钩子
 },
 *"lint-staged"*: {
  *"\*.{js,jsx,vue}"*: [
   "vue-cli-service lint",
   "git add"
  ]
 }
}
```

4、Commit message 的格式：

```js
<type>(<scope>): <subject>   // Header 是必需的
<body>             // Body 可以省略
<footer>            // Footer 可以省略
```

Header部分只有一行，包括三个字段：

- type（必需）：用于说明 commit 的类别，标识为：
  - feat：新功能（feature）
  - fix：修补 bug
  - docs：文档（documentation）
  - style： 格式（不影响代码运行的变动）
  - refactor：重构（即不是新增功能，也不是修改 bug 的代码变动）、
  - test：增加测试
  - chore：构建过程或辅助工具的变动
- scope（可选）：用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。
- subject（必需）：是 commit 目的的简短描述，不超过 50 个字符：
  - 以动词开头，使用第一人称现在时，比如 change，而不是 changed 或 changes
  - 第一个字母小写
  - 结尾不加句号（.）

## 三、选配依赖包

### 1、element-ui

下载依赖包：

```
yarn add element-ui
```

修改 src 目录下的 main.js 文件，全局引用：

```js
import Vue from "vue";
import App from "./App.vue";
import router from "./router";
import store from "./store";
import ElementUI from "element-ui";          // 引入 element-ui
import "element-ui/lib/theme-chalk/index.css";     // 全局引用样式
Vue.use(ElementUI);                  // 全局使用 element-ui
Vue.config.productionTip = false;
new Vue({
 router,
 store,
 render: *h* *=>* h(App)
}).$mount("#app");
```

使用方法详见文档：[element-ui 官方文档](https://element.eleme.cn/)

### 2、nprogress

下载依赖包：

```
yarn add nprogress
```

[github 传送门](https://github.com/rstacruz/nprogress)

### 3、svg-sprite-loader

下载依赖包：

```
yarn add -D svg-sprite-loader
```

新建 / 修改 vue.config.js：

```js
*const* path = require("path");
*function* resolve(*dir*) {
 return path.join(__dirname, *dir*);
}
*module*.*exports* = {
 chainWebpack: *config* *=>* {
  // set svg-sprite-loader
  *config*.module
   .rule("svg")
   .exclude.add(resolve("src/plugins/icons/svg"))
   .end();
  *config*.module
   .rule("icons")
   .test(/\.svg$/)
   .include.add(resolve("src/plugins/icons/svg"))
   .end()
   .use("svg-sprite-loader")
   .loader("svg-sprite-loader")
   .options({
    symbolId: "icon-[name]"
   })
   .end();
 }
};
```

src 目录下新增 plugins 目录，plugins 目录下新增 icons 目录：

@/plugins/icons/index.js：

```js
import svg from "./index.vue";
export default {
 install: *function*(*Vue*) {
  *const* requireAll = *requireContext* *=>*
   *requireContext*.keys().map(*requireContext*);
  *const* req = require.context("./svg", false, /\.svg$/);
  requireAll(req);
  *Vue*.component(svg.name, svg);
 }
};
```

@/plugins/icons/index.vue：

```js
<template>
  <svg class="svg-icon" :class="svgClass">
    <use :xlink:href="iconName"></use>
  </svg>
</template>

<script>
export default {
  name: "IconSvg",
  props: {
    svgClass: {
      type: String
    },
    svgName: {
      type: String,
      required: true
    }
  },
  computed: {
    iconName() {
      return `#icon-${this.svgName}`;
    }
  }
};
</script>

<style lang="less">
.svg-icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
</style>
```



@/plugins/icons/svg目录：（存放 svg 文件）

![](specification/svg.png)



svg 文件参考：

```js
<?xml version="1.0" standalone="no"?><!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd"><svg t="1583823818343" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="14478" width="200" height="200"><path d="M908.864 206.656c-11.52-12.096-24.768-18.432-39.872-19.072h-10.88c-38.08 6.08-71.36 11.968-99.776 17.664-28.416 5.76-51.392 11.648-68.928 17.664-14.528 2.432-34.624 9.536-60.288 21.312-25.728 11.776-55.744 27.648-90.24 47.616-17.536 6.08-35.072 6.08-52.608 0-35.072-16.896-65.024-31.424-89.792-43.52l-55.296-25.408c-19.968-6.016-43.84-11.904-71.616-17.664-27.84-5.76-61.952-11.648-102.464-17.664h-11.776c-17.536 0.64-31.424 6.976-41.728 19.072-10.304 12.096-15.744 27.2-16.32 45.312v440.704c0.576 15.104 5.76 28.736 15.424 40.832 9.664 12.096 22.08 19.648 37.184 22.656 40.512 6.016 76.48 12.864 107.904 20.416 31.424 7.552 57.408 14.336 78.016 20.416l59.84 23.552c25.408 9.664 55.616 23.296 90.688 40.832 17.536 5.44 35.072 5.44 52.608 0 34.432-17.536 64.512-31.872 90.24-43.072 25.664-11.2 45.824-18.304 60.288-21.312 20.544-5.44 46.528-11.52 78.016-18.112l107.904-22.656c14.528-3.008 26.752-9.984 36.736-20.864 9.984-10.88 14.976-25.088 14.976-42.624V251.968c-0.704-18.112-6.72-33.216-18.24-45.312z m-364.48 472.768c0 15.872-14.464 28.928-32.128 28.928s-32.128-12.992-32.128-28.928V433.856c0-15.872 14.464-28.928 32.128-28.928s32.128 12.992 32.128 28.928v245.568z" p-id="14479"></path></svg>
```

修改 src 目录下的 main.js 文件，全局引用：

```js
import IconSvg from "./plugins/icons";
Vue.use(IconSvg);
```

使用方法：

```js
<icon-svg
 svg-class="homework-svg-class"    // 可选，给对应的 svg 设 class 类名
 svg-name="homework"    // 必选，svg 对应的文件名称（@/plugins/icons/svg目录下）
>
</icon-svg>
```

### 4、vue-i18n

下载依赖包：

```js
yarn add vue-i18n
```

在 src 目录下新建 locales 文件夹，对 i18n 进行统一封装：

@/locales/i18n.js：

```js
import Vue from "vue";
import VueI18n from "vue-i18n";
import enUS from "./element/en-US";  //也可直接引用 element-ui 插件中的多语言文件
import zhCN from "./element/zh-CN";
import frFR from "./element/fr-FR";

Vue.use(VueI18n);

const LOCALE = "locale";                                     //本地存储语言的key
export const SUPPORTED_LANGS = ["en-US", "zh-CN", "fr-FR"];  //支持的语言
const DEFAULT_LANG = SUPPORTED_LANGS[0];                     //默认语言

export const setLocale = lang => localStorage.setItem(LOCALE, lang);
export const getLocale = () => {
  const lang = localStorage.getItem(LOCALE);
  return lang && SUPPORTED_LANGS.includes(lang) ? lang : DEFAULT_LANG;
};

const ELEMENT_LANG = {               //项目支持的语言与引入的ui多语言文件建立关联
  "en-US": enUS,
  "zh-CN": zhCN,
  "fr-FR": frFR
};

function loadLocaleMessages() {
  const locales = require.context("./项目名", true, /[A-Za-z0-9-_,\s]+\.json$/i);
  const messages = {};
  locales.keys().forEach(key => {
    const matched = key.match(/([A-Za-z0-9-_]+)\./i);
    if (matched && matched.length > 1) {
      const locale = matched[1];
      messages[locale] = {
        ...locales(key),
        ...ELEMENT_LANG[locale]      //多语言信息中加入ui多语言
      };
    }
  });
  return messages;
}

export default new VueI18n({
  locale: getLocale(),
  fallbackLocale: DEFAULT_LANG,
  messages: loadLocaleMessages()
});
```



@/locales/项目名/支持语言的 json 格式文件（en-US.json、zh-CN.json、fr-FR.json）：

```js
{
 "key": "value"
}
```

在 main.js 中引入 i18n：

```js
import Vue from "vue";
import App from "./App.vue";
import i18n from "./locales/i18n";
import ElementUI from "element-ui";
import "element-ui/lib/theme-chalk/index.css";

Vue.use(ElementUI, { i18n: (key, value) => i18n.t(key, value) }); //引入i18n

Vue.config.productionTip = false;

new Vue({
  i18n,
  render: h => h(App)
}).$mount("#app");
```

在 vue 实例中使用：

```js
<template>
  <div>
    <div>{{ info }}</div>
    <div>{{ $t("key.value") }}</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      info: this.$i18n.t("key.value")
    };
  }
};
</script>

```

在 vue 实例外使用：

```js
import i18n from "@/locales/i18n";

i18n.t("key.value")
```

### 5、axios

[github 传送门](https://github.com/axios/axios)

下载依赖包：

```
yarn add axios
```



新建 global 文件夹，目录结构如图：

![](specification/global.png)

@/global/request/api.js：

```js
export default {
  userInfo: "/api/xxxx",

  coursesAll: "/api/courses/all",
  courses: "/api/courses",
  coursesItem: id => `/api/courses/${id}`,
};
```

@/global/request/axios.js：

```js
import axios from "axios";
import { Message } from "element-ui";
import i18n from "@/locales/i18n";

const axiosInstance = axios.create();

axiosInstance.defaults.timeout = 20000;

axiosInstance.interceptors.request.use(
  config => {
    const newConfig = { ...config };
    const TOKEN = localStorage.getItem("admin_token");
    const lang = localStorage.getItem("locale");
    // const offset = -new Date().getTimezoneOffset() / 60;
    // const timezone = offset < 0 ? "UTC" + offset : "UTC+" + offset;
    if (TOKEN) {
      newConfig.headers.Authorization = `Bearer ${TOKEN}`;
    }
    newConfig.headers["Accept-Language"] = lang ? lang : "en-US";
    // newConfig.headers["Client-Timezone"] = timezone;
    return newConfig;
  },
  error => Promise.reject(error)
);

const handleErrorRequest = error => {
  const { response } = error;
  const status = response ? response.status : 408;

  if (response) {
    const { data } = response;
    const { errors } = data;
    const message = data.message || i18n.t("api.serverError");
    if (status === 401) {
      localStorage.removeItem("admin_token");
    } else if (status === 403) {
      Message.error(i18n.t("api.forbidden"));
    } else if (status === 419) {
      // 页面过期;
      localStorage.clear();
      sessionStorage.clear();
    } else if (status === 422 || status === 423 || status === 429) {
      let errorObj = errors ? errors : data;
      Object.keys(errorObj).forEach(errorKey => {
        const errorValue = errorObj[errorKey].join("\n");
        Message.error(errorValue);
      });
    } else {
      Message.error(message);
    }
  } else {
    Message.error(i18n.t("api.timeout"));
  }
};

axiosInstance.interceptors.response.use(
  res => {
    switch (true) {
      case res.status === 200:
        return res.data;
      case res.data.code === 200:
        return res.data.data;
      case res.data.error_code === 0:
        return res.data.data;
      // case !!res.status:
      //   Message.error({
      //     dangerouslyUseHTMLString: true,
      //     message: `<pre>${res.data.message}</pre>`
      //   });
      //   return Promise.reject(res.data);
      default:
        return res.data;
    }
  },
  error => {
    handleErrorRequest(error);
    return Promise.reject(error);
  }
);

export default {
  post(url = "", data = {}, config = {}) {
    return axiosInstance.post(url, data, config);
  },
  put(url = "", data = {}, config = {}) {
    return axiosInstance.put(url, data, config);
  },
  patch(url = "", data = {}, config = {}) {
    return axiosInstance.patch(url, data, config);
  },
  get(url = "", params = {}, config = {}) {
    const OPTIONS = Object.assign({ params }, config);
    return axiosInstance.get(url, OPTIONS);
  },
  delete(url = "", params = {}, config = {}) {
    const OPTIONS = Object.assign({ params }, config);
    return axiosInstance.delete(url, OPTIONS);
  }
};
```

@/global/service/user.js：

```js
import axios from "@/global/request/axios";
import API from "@/global/request/api";

const userService = {
  info() {
    return axios.get(API.userInfo);
  }
};

export default userService;
```



### 四、src 目录架构

### 1、main.js

只存放全局使用的组件，整体尽量简洁。

### 2、App.vue

渲染父路由，存放全局样式，样式分类抽离进 assets 目录下的 styles 目录中：

```js
<template>
  <router-view></router-view>
</template>

<style lang="less">
@import "./assets/styles/base.less";
@import "./assets/styles/el.less";
</style>

```



### 3、assets

存放静态文件，一般为样式和图片，所以分别新建两个目录：images 和 styles：

![](specification/assets.png)

base.less 与 el.less 具体参考如下：

```js
// base.less
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
  font-family: "Helvetica Neue", Helvetica, "PingFang SC", "Hiragino Sans GB",
    "Microsoft YaHei", "微软雅黑", Arial, sans-serif;
}

html, body {
  height: 100%;
  background-color: #fafafa;
}

a {
  text-decoration: none;
}

.page-content-container {
  padding: 12px 20px;
}

.mb-24 {
  margin-bottom: 24px;
}
.mb-20 {
  margin-bottom: 20px;
}
.mb-12 {
  margin-bottom: 12px;
}
.mb-8 {
  margin-bottom: 8px;
}
.mr-12 {
  margin-right: 12px;
}

.w-90pc {
  width: 90% !important;
}
.w-95pc {
  width: 95% !important;
}
.w-600 {
  width: 600px !important;
}
.w-260 {
  width: 260px !important;
}
.w-200 {
  width: 200px !important;
}
.w-180 {
  width: 180px !important;
}
.w-170 {
  width: 170px !important;
}
.w-135 {
  width: 135px !important;
}
.w-120 {
  width: 120px !important;
}
.w-100 {
  width: 100px !important;
}
.w-90 {
  width: 90px !important;
}

.brand-color {
  color: #409EFF;
}
.success-color {
  color: #13ce66;
}
.warning-color {
  color: #E6A23C;
}
.danger-color {
  color: #F56C6C;
}
.info-color {
  color: #909399;
}
.normal-info-color {
  color: #606266;
}
.deep-info-color {
  color: #303133;
}
```

```js
// el.less
// el文字链接 字重
.el-link--inner {
  font-size: 12px;
  font-weight: 400;
}


// el表格
.el-table {
  tr th,
  tr td {
    padding: 4px;
    font-size: 12px;
  }
}


// el分页 外边距
.el-pagination {
  margin-top: 12px;
}


// el卡片 header内容位置、body内容位置
.el-card {
  .el-card__header {
    padding: 16px 20px 8px;
  }
  .el-card .el-card__body {
    padding: 8px 20px 16px;
  }
}


// el多行文本 字数统计调整
.el-textarea .el-input__count {
  bottom: -8px !important;
  background: transparent !important;
}


// el日期选择器 选择范围时的分隔符宽度调整
.el-range-separator {
  width: 10% !important;
}


// el多选框 禁用状态 已选颜色加深
.el-checkbox.is-disabled.is-checked {
  .el-checkbox__label {
    color: #606266;
  }
  .el-checkbox__input.is-disabled.is-checked {
    .el-checkbox__inner {
      border-color: #606266;
      &::after {
        border-color: #606266;
      }
    }
  }
}


// el输入框、选择器
.el-input__inner:hover,
.el-textarea__inner:hover,
.el-select:hover .el-input__inner {
  border-color: #409EFF;
}
.el-input.is-disabled .el-input__inner,
.el-textarea.is-disabled .el-textarea__inner {
  color: #606266 !important;
  border: none !important;
  cursor: default !important;
}


// el-upload 禁用状态 鼠标光标
.upload-container.disabled {
  .el-upload {
    cursor: default;
  }
  img {
    cursor: default;
  }
}


// el菜单导航
.el-menu {
  border-right: 0 !important;
  .el-submenu,
  .el-menu-item {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }
}

.el-menu-item,
.el-submenu__title {
  height: 48px !important;
  line-height: 48px !important;
}

.el-submenu-level_2 .el-submenu__title {
  padding: 0 20px !important;
}

.el-menu-item {
  padding: 0 20px !important;
  .fa-dot {
    display: inline-block;
    margin-right: 6px;
    width: 24px;
    font-size: 30px;
    text-align: center;
    vertical-align: top;
    color: #909399;
    &:before {
      content: "\00b7";
    }
  }
  &:hover .fa-dot {
    color: #EEE;
  }
}
.el-menu-item.is-active .fa-dot {
  color: #409EFF;
}
.el-submenu-level_1.is-active > .el-submenu__title {
  i {
    color: #409EFF !important;
  }
  svg {
    color: #409EFF !important;
  }
  span {
    color: #409EFF !important;
  }
}
.fa-diamond {
  display: inline-block;
  margin-right: 6px;
  width: 24px;
  font-size: 16px;
  text-align: center;
  vertical-align: top;
  color: #909399;
  transform-origin: center 53%;
  transform: rotateZ(135deg);
  transition: all 0.4s linear;
  &:before {
    content: "\2724";
  }
}
.el-submenu-level_2:hover .fa-diamond {
  color: #EEE;
  transform: rotate(0deg);
}
.el-submenu-level_2.is-active .el-submenu__title {
  span {
    color: #409EFF !important;
  }
  .fa-diamond {
    transform: rotate(0deg);
  }
}
```

### 4、components

存放所有的组件（可复用组件、特殊封装组件）

TheLayout.vue 组件参考：

```js
<template>
  <div class="page">
    <div class="page-left-container" :style="{ width: siderWidth }">
      <div class="page-sidebar">
        <div class="sidebar-header">
          <img class="sidebar-header-logo" src="@/assets/images/logo.png" />
        </div>
        <div
          class="sidebar-body"
          :style="{ backgroundColor: navBackgroundColor }"
        >
          <el-scrollbar class="scrollbar-section">
            <el-menu
              :background-color="navBackgroundColor"
              :text-color="navTextColor"
              :active-text-color="navActiveTextColor"
              :default-active="navDefaultActive"
              :router="true"
              :collapse="collapse"
            >
              <template v-for="route in navRoutes">
                <el-menu-item
                  v-if="!haveChildren(route)"
                  :key="route.name"
                  :index="route.name"
                  :route="{ name: route.name }"
                >
                  <i
                    v-if="route.meta.nav.icon"
                    :class="route.meta.nav.icon"
                  ></i>
                  <icon-svg
                    v-if="route.meta.nav.svg"
                    :svg-class="route.meta.nav.svg.class"
                    :svg-name="route.meta.nav.svg.name"
                  ></icon-svg>
                  <span slot="title">{{ route.meta.nav.title }}</span>
                </el-menu-item>
                <el-submenu
                  class="el-submenu-level_1"
                  v-else
                  :key="route.name"
                  :index="route.name"
                >
                  <template slot="title">
                    <i
                      v-if="route.meta.nav.icon"
                      :class="route.meta.nav.icon"
                    ></i>
                    <icon-svg
                      v-if="route.meta.nav.svg"
                      :svg-class="route.meta.nav.svg.class"
                      :svg-name="route.meta.nav.svg.name"
                    ></icon-svg>
                    <span>{{ route.meta.nav.title }}</span>
                  </template>

                  <template v-for="item in route.children">
                    <el-menu-item
                      v-if="!haveChildren(item)"
                      :key="item.name"
                      :index="item.name"
                      :route="{ name: item.name }"
                    >
                      <span class="fa-dot"></span>
                      <span>{{ item.meta.nav.title }}</span>
                    </el-menu-item>
                    <el-submenu
                      class="el-submenu-level_2"
                      v-else
                      :key="item.name"
                      :index="item.name"
                    >
                      <template slot="title">
                        <span class="fa-diamond"></span>
                        <span>{{ item.meta.nav.title }}</span>
                      </template>
                      <el-menu-item
                        v-for="data in item.children"
                        :key="data.name"
                        :index="data.name"
                        :route="{ name: data.name }"
                      >
                        <span class="fa-dot"></span>
                        <span>{{ data.meta.nav.title }}</span>
                      </el-menu-item>
                    </el-submenu>
                  </template>
                </el-submenu>
              </template>
            </el-menu>
          </el-scrollbar>
        </div>
        <div class="sidebar-footer">
          <div class="collapse-icon-container" @click="handleCollapse">
            <img
              class="collapse-icon"
              :class="{ unfold: collapse }"
              src="@/assets/images/fold.svg"
            />
          </div>
        </div>
      </div>
    </div>
    <div class="page-right-container">
      <div class="page-header">
        <div class="page-header-left"></div>
        <div class="page-header-middle"></div>
        <el-dropdown style="height: 100%" @command="handleCommand">
          <div class="page-header-right">
            <img v-if="userAvatar" class="user-avatar" :src="userAvatar" />
            <img
              v-else
              class="user-avatar"
              src="@/assets/images/default-avatar.png"
            />
            <span class="user-name">{{ userName }}</span>
            <i class="el-icon-arrow-down"></i>
          </div>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item icon="el-icon-video-pause" command="handleLogout"
              >登出</el-dropdown-item
            >
          </el-dropdown-menu>
        </el-dropdown>
      </div>
      <div class="page-content">
        <el-scrollbar class="scrollbar-section">
          <router-view />
        </el-scrollbar>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from "vuex";
import routesAll from "@/router/routes";

export default {
  data() {
    return {
      navBackgroundColor: "#141f29",
      navTextColor: "#EEE",
      navActiveTextColor: "",
      navDefaultActive: this.$route.name,
      navRoutes: [],
      collapse: false
    };
  },
  computed: {
    ...mapState(["userAvatar", "userName", "authPermissions"]),
    siderWidth() {
      return this.collapse ? "64px" : "227px";
    }
  },
  created() {
    this.getRoutes();
  },
  methods: {
    getRoutes() {
      let resultRoutes = this.filterPermissionRoutes(
        routesAll,
        this.authPermissions
      );
      this.navRoutes = this.filterRoutes(resultRoutes);
    },
    filterPermissionRoutes(routes, permissions) {
      const filterRoutes = [];
      routes.forEach(data => {
        const route = { ...data };
        const notPermission = !route.permission;
        const hasPermission = permissions.includes(route.permission);
        const passPermission = notPermission || hasPermission;
        let hasPath = true;
        if (route.children) {
          route.children = this.filterPermissionRoutes(
            data.children,
            permissions
          );
          if (route.children.length === 0) {
            hasPath = false;
          }
        }
        if (passPermission && hasPath) {
          filterRoutes.push(route);
        }
      });
      return filterRoutes;
    },
    filterRoutes(routes) {
      let result = [];
      routes.forEach(data => {
        if (data.meta && data.meta.nav) {
          let item = {
            name: data.name,
            meta: data.meta
          };
          if (data.children) {
            item.children = this.filterRoutes(data.children);
          }
          result.push(item);
        } else if (data.children) {
          this.filterRoutes(data.children).forEach(item => {
            result.push(item);
          });
        }
      });
      return result;
    },
    haveChildren(route) {
      let children = route.children || [];
      return children.length;
    },
    handleCollapse() {
      this.collapse = !this.collapse;
    },
    handleCommand(command) {
      this[command]();
    },
    handleLogout() {
      this.$store.dispatch("logout");
    }
  }
};
</script>

<style lang="less">
.page {
  .scrollbar-section {
    height: 100%;
    .el-scrollbar__wrap {
      overflow-x: auto;
      min-height: 100%;
    }
    .el-scrollbar__view {
      min-height: 100%;
    }
  }
}
</style>
<style lang="less" scoped>
.nav-svg {
  margin-right: 5px;
  width: 24px;
  height: 18px;
  vertical-align: middle;
  color: #909399;
}
.page {
  height: 100vh;
  display: flex;
  .page-left-container {
    z-index: 101;
    flex: none;
    display: flex;
    flex-direction: column;
    width: 246px;
    transition: all 0.2s ease-out;
    .page-sidebar {
      flex: 1;
      display: flex;
      flex-direction: column;
      .sidebar-header {
        z-index: 102;
        flex: none;
        height: 48px;
        background: #fff;
        .sidebar-header-logo {
          margin: 8px 12px 8px 20px;
          height: 32px;
        }
      }
      .sidebar-body {
        flex: 1;
        height: 0;
        overflow: auto;
        box-shadow: 2px 2px 6px rgba(0, 21, 41, 0.35);
      }
      .sidebar-footer {
        flex: none;
        padding: 8px;
        height: 48px;
        background: #141f29;
        overflow: hidden;
        box-shadow: 2px 0 6px rgba(0, 21, 41, 0.35);
        .collapse-icon-container {
          height: 32px;
          text-align: center;
          background-color: #1d2b3a;
          border-radius: 3px;
          cursor: pointer;
          &:hover {
            background-color: #273849;
          }
          .collapse-icon {
            margin-top: 6px;
            width: 20px;
            height: 20px;
            transition: all 0.2s ease-out;
            &.unfold {
              transform: rotate(-180deg);
            }
          }
        }
      }
    }
  }
  .page-right-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    min-width: 1000px;
    overflow: hidden;
    .page-header {
      z-index: 100;
      display: flex;
      justify-content: space-between;
      padding: 0 20px;
      height: 48px;
      font-size: 12px;
      line-height: 48px;
      font-weight: 400;
      color: #606266;
      background: #fff;
      box-shadow: 0 1px 2px 0 rgba(9, 18, 26, 0.06),
        0 4px 8px 0 rgba(39, 56, 73, 0.08);
      .page-header-right {
        display: flex;
        align-items: center;
        padding: 0 10px;
        height: 100%;
        transition: all 0.2s ease;
        cursor: pointer;
        &:hover {
          background: rgba(0, 0, 0, 0.025);
        }
        .user-avatar {
          margin: 0 8px;
          width: 30px;
          height: 30px;
          object-fit: cover;
          border-radius: 50%;
        }
        .user-name {
          margin-right: 8px;
          font-size: 12px;
          vertical-align: middle;
        }
      }
    }
    .page-content {
      position: relative;
      flex: 1;
      overflow-y: auto;
      background: #f0f2f5;
    }
  }
}
</style>
```

面包屑 BreadCrumb.vue 组件参考：

```js
<template>
  <div class="breadcrumb-section">
    <el-breadcrumb
      class="breadcrumb-list"
      v-if="breadcrumbValue.length"
      separator-class="el-icon-arrow-right"
    >
      <template v-for="(item, index) in breadcrumbValue">
        <el-breadcrumb-item
          v-if="item.to"
          :to="item.to"
          :key="index"
          :replace="true"
          >{{ item.name }}</el-breadcrumb-item
        >
        <el-breadcrumb-item v-else :key="item.name">{{
          item.name
        }}</el-breadcrumb-item>
      </template>
    </el-breadcrumb>
  </div>
</template>

<script>
export default {
  props: {
    breadcrumb: {
      type: Array,
      default: () => []
    }
  },
  computed: {
    breadcrumbValue() {
      const { breadcrumb } = this;
      if (breadcrumb.length) {
        return breadcrumb;
      }
      return this.$route.matched
        .filter(data => data.meta && data.meta.breadcrumb)
        .map(data => ({
          name: data.meta.breadcrumb.title,
          to: data.meta.breadcrumb.replace ? { name: data.name } : undefined
        }));
    }
  }
};
</script>

<style lang="less" scoped>
.el-breadcrumb {
  margin-left: 4px;
  margin-bottom: 12px;
  font-size: 12px;
  line-height: 16px;
}
</style>
```

### 5、plugins

存放所有与 devDependencies 相关的插件

### 6、router

路由分离，路由运行与路由结构分离成 index.js 与 routes.js，路由结构可根据菜单模块进一步分离，分离后的文件名均加上 routes 前缀：

![](specification/router.png)

index.js：

```js
import Vue from "vue";
import VueRouter from "vue-router";
import routes from "./routes";
import NProgress from "nprogress";                  // 引入 NProgress
import "nprogress/nprogress.css";                   // 引用 NProgress 样式
import userService from "@/global/service/user";    // 引入封装好的 user 请求
import Store from "@/store/index";                  // 引入 全局 store

Vue.use(VueRouter);

const router = new VueRouter({
  mode: "history",                                  // 使用路由的 history 模式
  routes                                            // 路由的结构分离到 routes 文件
});

router.firstInit = false;                           // 用于判断是否第一次渲染

router.beforeEach(async (to, from, next) => {
  NProgress.start();                                    // NProgress 的使用
  if (to.meta.title) document.title = to.meta.title;    // 页面标题
  try {
    const TOKEN = localStorage.getItem("admin_token");
    if (!TOKEN && to.name !== "AccountLogin") {
      next({ name: "AccountLogin", replace: true });
      return;
    }
    if (TOKEN && to.name === "AccountLogin") {
      next({ name: "Home", replace: true });
    }
    if (!router.firstInit && TOKEN) {
      router.firstInit = true;
      const userInfo = await userService.info();        // 初次渲染时将用户基本信息
      Store.commit("getUserAvatar", userInfo.avatar);   // 和所拥有的权限存入 store
      Store.commit("getUserName", userInfo.name);
      Store.commit("getAuthPermissions", userInfo.permissions);
    }
    next();
  } catch {
    next();
  }
});

router.afterEach(() => {
  NProgress.done();                                     // NProgress 的使用
});

export default router;
```

routes：

```js
import routesHome from "./routesHome";

import TheLayout from "@/components/TheLayout.vue";

export default [
  {
    path: "/admin",
    component: TheLayout,
    redirect: { name: "Home" },
    children: [...routesHome]
  }
];
```



### 7、store

存放全局信息、方法（一般为用户基本信息与用户权限）

### 8、views

存放所有页面，注意命名规范，可参考 [vue 风格指南](https://cn.vuejs.org/v2/style-guide)

![](specification/views.png)

页面 script 中 export default 选项顺序：

```js
export default {
  components: {},
  props: {},
  data() {
    return {};
  },
  computed: {},
  created() {},
  mounted() {},
  watch: {},
  methods: {}
};
```

### 9、utils

存放一些小工具小功能小方法，如：

```js
const formatTime = timestamp => {
  const date = new Date(timestamp);
  const year = date.getFullYear();
  const month = date.getMonth() + 1;
  const day = date.getDate();
  const hour = date.getHours();
  const minute = date.getMinutes();
  const second = date.getSeconds();
  return (
    [year, month, day].map(formatNumber).join("/") +
    " " +
    [hour, minute, second].map(formatNumber).join(":")
  );
};

const formatNumber = n => {
  n = n.toString();
  return n[1] ? n : "0" + n;
};

const formateDay = timestamp => {
  const date = new Date(timestamp);
  const year = date.getFullYear();
  const month = date.getMonth() + 1;
  const day = date.getDate();
  return [year, month, day].map(formatNumber).join("-");
};

module.exports = {
  formatTime: formatTime,
  formateDay: formateDay
};
```

## 五、Demo

[github 传送门](https://github.com/WinstonYa/vue-admin-framework)