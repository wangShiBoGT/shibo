---
title: vue2和vue3的优劣势
date: 2022-5-13
cover: https://cdn.jsdelivr.net/gh/wangShiBoGT/drawingBed/blogImages/c071cdc46f0c4867a1d52d0cb51fc6d6.jpg
tags:

- vue
categories:
- 技术笔记

---

::: tip 摘要
vue2和vue3的区别<br>
:::

<!-- more -->

### 一、生命周期

从Vue2和Vue3生命周期的区别来详细说明:

1. Vue2的生命周期函数

- beforeCreate:组件刚刚被创建,还未初始化数据和事件
- created:组件创建完成,属性方法都可以使用,但还未渲染DOM
- beforeMount:模板已经在内存中编译完成,但尚未渲染
- mounted:内存中的模板已经真实渲染到页面中,现在可以通过DOM操作
- beforeUpdate:组件更新之前,页面尚未重新渲染
- updated:组件更新完毕,页面也重新渲染了
- beforeDestroy:组件还未被销毁,状态仍然可用
- destroyed:组件已经被销毁,不再可用

2. Vue3的生命周期函数

- beforeCreate改名为setup
- created改名为setup
- beforeMount改名为onBeforeMount
- mounted改名为onMounted
- beforeUpdate改名为onBeforeUpdate
- updated改名为onUpdated
- beforeDestroy改名为onBeforeUnmount
- destroyed改名为onUnmounted

3. 其他区别

- Vue3没有了beforeCreate和created函数
- 所有生命周期函数现在需要通过import方式显式引入
- 新增了两种函数组合composition API

总结:
Vue3对生命周期函数做了重命名,删除了beforeCreate和created,同时引入了composition API。核心区别在于引入了setup函数取代了之前的created和beforeCreate,增加了组合式函数。

### 二、选项式API和组合式API

与我们在vue2开发时的选项式API相比，组合式API提供了更高的灵活性和可复用性。在选项式API中,我们需要按照**data**、**methods**、**watch**等选项来组织代码,使得开发人员能够按部就班的进行开发,流程简单,但也正因为按部就班式的开发,使得我们开发较为复杂的组件时,相关的逻辑会被分散在不同的选项中使得代码维护起来较为繁杂。

在组合式API中,可以把所有代码写到setup函数内,使得我们可以按照逻辑关系来组织代码,简单来说就是数据,方法,watch等,可以写在一起,后期方便维护,阅读起来也非常容易,同时组合式API也有一些缺点: 写惯了选项式API的人需要一定时间转换,相对提高了一定的门槛。

选项式代码示例:

```javascript

<template>
    <div></div>
</template>

<script>
export default {
  name: 'XXX',
  data() {
    return {}
  },
  created() {},
  methods: {}
}
</script>

```

组合式代码示例:

```javascript
<template>
  <div class=""></div>
</template>

<script setup>
import {} from "vue"
</script>

<style lang="scss" scoped></style>

```

### 三、vue2的响应式和vue3响应式的区别

Vue的响应式系统是其核心功能之一,让我们可以通过修改数据来自动更新UI。从Vue2到Vue3,响应式系统有了很大的改进。
**Vue 2的响应式**
Vue 2中,使用Object.defineProperty()来实现响应式。这种方法有一些限制:

- 只能监听属性,不能监听对象新增属性
- 只能监听到对象属性的变化,无法监听到数组索引和长度的变化

**Vue 3的响应式**
Vue 3使用Proxy完全重写了响应式系统,解决了Vue 2的痛点。

- 可以监听动态新增的属性
- 可以正确监听数组变化
- 提供了reactive API来代替Vue 2的Vue.observable()

另外,Vue 3还提供了ref API来Generating响应式的基本数据类型如string、number等。

**总结**
Vue的响应式系统让数据驱动视图,是其核心优势之一。从Vue2到Vue3有了重大改进,使响应式系统变得更加完善强大。这为我们构建复杂应用提供了便利。

### 三、从vue2到vue3升级,为什么官方推荐pinia

>pinia:新一代的状态管理工具

Pinia是Vue生态中的轻量级状态管理库,可以非常方便地在Vue3项目中实现状态管理。以下几点原因解释了为什么Pinia适合在Vue3项目中使用:

#### 1. 它是为Vue3设计的

Pinia是从零开始为Vue3设计的状态管理库,完全采用Composition API编写。这样可以发挥Vue3的全部 strengths,提供更好的用于Vue3的开发体验。

#### 2. 简单易用

相比Vuex,Pinia的API更加简洁,易于上手使用。只需要几行代码就可以快速创建一个store并实现状态管理。

#### 3. 模块化Store

Pinia支持将一个大Store拆分成多个模块,每个模块都具有响应式的状态管理能力。这种拆分有助于提高代码的可维护性。

#### 4. Devtools支持

Pinia内置了对Vue Devtools的支持,可以直观地在Devtools面板查看所有Store中的State,甚至可以时光旅行调试。

#### 5. 类型推导

Pinia可以自动推导State类型,无需手动声明。这提高了开发效率,也减少了出错的可能性。

#### 6. 轻量高效

相比Vuex,Pinia的体积更小,性能也更优异。这使其非常适合中大型Vue项目的状态管理。
综上所述,Pinia是专为Vue3设计的状态管理库,使用简单高效,非常适合在Vue3项目中使用。它可以帮助我们更好地构建复杂的Vue3应用。

pinia代码中使用:

```javascript
import { defineStore } from 'pinia'
import { ref } from 'vue'

type User = {
  name: string
  age: number
}

export const useUserStore = defineStore('user', () => {
  // 相当于 vuex 中的 state
  state: () => {
    return {
      name: "李四",
      age: 25,
    };
  },
})

//在组件中使用
import { useUserStore } from './stores/index'

const store = useUserStore()
const { name, age } = store;

//在template中使用
<template>
  <h1>组件</h1>
  <p>姓名：{{ name }}</p>
  <p>年龄：{{ age }}</p>
  <button @click="changeName">更改姓名</button>
</template>
 //如想修改某个状态值的话
 const changeName = () => {
  store.name = "张三";
  console.log(store);
};
 //在pinia中使用getter,在state下方写入
 getters: {
    getAddAge: (state) => {
      return state.age + 100;
    },
  },
 //在组件中使用
 <template>
  <p>新年龄：{{ store.getAddAge }}</p>
  <button @click="patchStore">批量修改数据</button>
</template>
<script setup lang="ts">
import { useUserStore } from "../src/store/user";
const store = useUserStore();
const patchStore = () => {
  store.$patch({
    name: "张三",
    age: 100,
  });
};
</script>
//使用action,同理,在state下方添加:
actions: {
    saveName(name: string) {
      this.name = name;
    },
  },
  //在页面使用
  const saveName = () => {
  store.saveName("我是张三");
};
```

注意:getter和Vue中的计算属性类似,他也是被缓存的,可以在getter中处理state数据。
在上方代码中,定义了一个极其普通的actions方法,可以在actions里,处理逻辑,请求等...
