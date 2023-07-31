---
title: 基于vue3实现动态渲染菜单，权限控制vite版
date: 2022-12-17
cover: https://w.wallhaven.cc/full/l8/wallhaven-l83o92.jpg
tags:
 - vue3
categories:
 -  技术笔记
sticky: 1
---

::: tip 介绍
vue3+pinia(无TS版)菜单权限处理 动态路由<br>
:::

<!-- more -->

### 搭建前端项目模板

为了更好的方便说明功能，本文直接略过，（可以自己搭建，或在GitHub || gitee中查找适合自己的模板）

### 配置Pinia和创建Store
>
>Pinia是一个状态管理库，它提供了一种方便的方式来管理应用程序的状态。
>我们需要在项目中配置Pinia，并创建一个Store用于管理菜单权限和动态路由相关的状态和行为。

首先，在src目录下创建一个名为store的文件夹，然后在store文件夹中创建一个名为index.js的文件。在index.js中，我们需要做以下的配置和操作：

```javaScript
import { createPinia } from 'pinia';
/* 创建pinia实例 */
const pinia = createPinia();

export default pinia;
```

在store的文件夹中创建modules文件夹，然后在modules文件夹中创建一个名为permission.js的文件。

定义菜单权限和动态路由相关的状态
在我们的Store中，我们需要定义用于管理菜单权限和动态路由的状态。

```javaScript
state: () => ({
  menuList: [
    { name: '首页', path: '/home', permission: 'home:view' },
    { name: '用户管理', path: '/user', permission: 'user:view' },
    // 其他菜单项...
  ]
}),
```

然后，我们需要定义动态路由的状态，它也可以是一个数组，每个元素代表一个动态路由，包含路由路径、组件路径和对应的权限等信息:

```javaScript
state: () => ({
  routes: [
    { path: '/home', component: 'Home', permission: 'home:view' },
    { path: '/user', component: 'User', permission: 'user:view' },
    // 其他动态路由...
  ]
}),
```

### 在permission.js中实现菜单权限的处理

```javascript
const modules = import.meta.glob("../../views/**/**.vue");

//登录成功后调用路由接口拿到路由数据，对其处理
const filterAsyncRoutes = (routes, roles) => {
  const asyncRoutes = [];
  routes.forEach((route) => {
    const tmpRoute = { ...route };
    if (hasPermission(roles, tmpRoute)) {
      if (tmpRoute.component?.toString() == "Layout") {
        tmpRoute.component = Layout;
      } else {
        const component = modules[`../../views/${tmpRoute.component}.vue`];
        if (component) {
          tmpRoute.component = component;
        } else {
          tmpRoute.component = modules[`../../views/error-page/404.vue`];
        }
      }
      if (tmpRoute.children) {
        tmpRoute.children = filterAsyncRoutes(tmpRoute.children, roles);
      }
      asyncRoutes.push(tmpRoute);
    }
  });

  return asyncRoutes;
};

function generateRoutes(roles) {
    return new Promise((resolve, reject) => {
      // 接口获取所有路由
      listRoutes()
        .then(({ data: asyncRoutes }) => {
          const accessedRoutes = filterAsyncRoutes(asyncRoutes, roles);
          setRoutes(accessedRoutes);
          resolve(accessedRoutes);
        })
        .catch((error) => {
          reject(error);
        });
    });
  }
  return { routes, setRoutes, generateRoutes };
```

其中这里需要注意,由于vite的使用，我们无法使用webpack提供的require.context了。Vite 支持使用特殊的 import.meta.glob 函数从文件系统导入多个模块。这只是一个 Vite 独有的功能而不是一个 Web 或 ES 标准。

```javascript
const modules = import.meta.glob("../../views/**/**.vue");
// 经验之谈： 注意路由被动态引入后，每一个文件所依赖或使用的，模块，都要确保是正确的，否则构建时将会报错
```

在pinia处理好后，拿到路由信息,添加到路由

```javascript
 filterRoutes.forEach(route => router.addRoute(route));
```

注意在调用完路由，且处理好后，需在permission中的router.beforeEach路由守卫中 添加：return to.fullPath; 添加动态路由后，必须加这一句触发重定向，否则会出不来页面
