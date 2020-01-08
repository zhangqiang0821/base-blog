---
title: 前端编程风格指南
date: 2017-11-03 10:49:41
tags: [风格,样式,约定]
categories: js
top: true
---

# 前端编程风格指南

> 本指南主要针对前端语法、代码风格、应用组织做了一些约定，每条约定尽量以目前使用较多，符合大多数人习惯为原则，并解释了为什么要这样做。相信在团队开发的项目中，大家遵循同样的约定，有利于代码的整洁、项目的稳定，保证项目的可读性、易维护性和扩展性。

<!-- more -->

# 目录
## [通用篇](#tongyong)
## [vue篇](#vue)
## [react篇](#react)

---

##  <a name="feature">通用篇</a>

### 命名
> 命名约定对可维护性和可读性非常重要。本指南为文件名、方法名、变量名推荐了一套命名约定。

#### 使用驼峰命名法
- 组件的文件名以大写开头（例如User.vue、User.jsx）
- 普通js文件以小写字母开头（例如index.js、 app.js）
- 多个单词用驼峰命名法（例如UserList.vue、HonePage.vue）
- 变量名、常量名和方法名统一小写开头（例如getUserList()）
##### *为什么*
- 小写开头的驼峰命名比蛇形命名（例如USER_LIST）容易阅读
- 将常亮名大写是比较过时的做法，当初是为了便于区分常亮和防止常亮被更改，而现在的ide工具的智能提示加上同const定义常亮已经能避免这些问题

#### 使用统一的后缀
- 为同一种类型的文件定义同样的后缀（例如Model、Router、Api、Utils）
- 统一类型的文件用相同的后缀（例如userModel、commonModel、userRouter、commonRouter）
##### *为什么*
- 统一的后缀一眼就能明白文件是做什么用的

### 应用结构
#### 复用
- 将核心模块剥离
- 将重复2次及以上的代码剥离

#### 识别性
- 做到看一眼目录名就能识别目录下面的文件是什么作用
- 文件夹层级不要过深，尽量扁平
- 配置、自动生成的文件不要和源码混在一起

### 编程约定
#### 用空行或注释行分割第三方导入和应用导入
```js
import Vue from 'vue'
// 本地导入
import router from './global/router'
import store from './global/store'
```
#### *为什么*
- 可以让阅读和定位本地导入更加容易
- 关注分离，一般情况我们不需要关心第三方的导入
#### 使用import/export导入和导出模块
- 不要再使用AMD、CMD的导出导入
#### *为什么*
- AMD、CMD是ES6模块方案之前的第三方方案

#### 不要污染全局作用域
- 如非必要，不要在window下添加任何的方法和属性

#### 选择使用async方法
- 在使用异步方法时，考虑使用async
- 不要再用callback

```js
  async testAsync () {
    const data = await this.$store.dispatch('testAsync')
    console.log(data)
  }
```

#### *为什么*
- 代码可读性更好
- 使异步操作更容易理解

#### 防止页面刷新丢失数据
- 不要在前一个页面查询下一个页面需要的数据，前一个页面可以通过路由传递简单的值给下个页面，如id
- 公用数据在最外层的组件初始化
- 考虑持久化store

#### 组件名预定
- 基础组件应该全部以一个特定的前缀开头（例如My、Base）
- 和父组件紧密耦合的子组件应该以父组件名作为前缀命名。

```js
components/
|- TodoList.vue
|- TodoListItem.vue
|- TodoListItemButton.vue
```

#### *为什么*
- 一眼就能区分基础组件
- 编辑器通常会按字母顺序组织文件，子组件以父组件的名字为前缀，这些关联的组件会被排列在一起

#### 用ES6的语法
- 能用ES6的地方不用ES5
- 用let、const替代var，能用const的地方不用let
- 用箭头函数
- 多用对象和数组解构
- 用模板字符串替代字符串的拼接
  
---

##  <a name="vue">Vue篇</a>

#### 尽可能的细分组件
- 尽量保证一个组件只做一件事
- 尽可能的复用组件，不一定完全一样的组件才能复用，有点细节的差异可以通过属性控制差异化的显示
#### *为什么*
- 每个组件只需要关注一件事，代码易读
- 每个vue文件不会太长，容易维护
- 复用的组件将大大增加代码的可维护性

#### css模块化
- 组件的style标签一定要加上scoped
- 全局的样式在入口文件导入
- 优先使用全局样式，可以用多个全局的样式组合出想要的样式

```html
<div class="f-12 c-red m-6 p-10"></div>
```

#### *为什么*
- 使用局部作用域的样式可以防止样式污染全局样式
- 使用全局样式可以减少局部样式，从而减小应用的体积
- 使用全局样式可以在有需要的时候统一调整样式

#### 按需使用全局store
- store只保存组件间需要共享的数据
- 父子组件的通信可以使用vue自有的方式来实现

```js
// 这个方法请求接口返回的数据只是当前组件使用，
// 直接通过then返回就好了，不用保存在store
async test () {
  const data = await this.$store.dispatch('searchFlight')
  console.log(data) 
}
```

#### *为什么*
- 减小store的体积，如果需要持久化store，这将很有用
- 太多的数据会让store变得难以维护和阅读

#### 善用计算属性
- 需要通过一些状态计算获得新状态的情况（如排序、过滤、转换等），选择使用计算属性
#### *为什么*
- 计算属性在依赖的属性值不改变的情况下，不会重复计算，性能比filter、watch要好

#### 不用使用refs、parent、children
- 不是万不得已，不要使用refs、parent、children
- 用vuex共享组件状态
- 用emit、on处理事件
- 用中央总线的方式处理跨组件的事件
#### *为什么*
- 会破坏单向数据流的概念
- 会加强让组件的耦合，不利于扩展和维护
会让代码变得难以阅读

#### 推荐的vue文件结构
- 总体按照模板、js、样式的顺序
- js文件中，组件、属性/状态、生命周期、方法的顺序
- 生命周期方法的顺序依照执行顺序排序
- 用webstorm的可以将下面的代码存为模板，每次新建一个vue文件，自动生成下面的代码，删掉用不到的部分就可以了

```html
<template>
</template>

<script>
  import {mapState} from 'vuex'

  export default {
    components: {},
    props: {},
    data () {
      return {}
    },
    computed: {
      ...mapState([])
    },
    beforeCreate () {},
    created () {},
    beforeMount () {},
    mounted () {},
    beforeUpdate () {},
    updated () {},
    beforeDestroy () {},
    destroyed () {},
    methods: {},
    filters: {}
  }
</script>

<style scoped>
</style>
```

#### *为什么*
- 使用统一的文件结构有利于提高代码可阅读性

---

##  <a name="react">React篇</a>

#### 尽可能的细分组件
- 尽量保证一个组件只做一件事
- 尽可能的复用组件，不一定完全一样的组件才能复用，有点细节的差异可以通过属性控制差异化的显示
- 不要将每个组件都写成容器组件，一般情况下当前路由最外层的组件为容器组件，负责从store获取状态，子组件为展示组件
#### *为什么*
- 每个组件只需要关注一件事，代码易读
- 每个jsx文件不会太长，容易维护
- 复用的组件将大大增加代码的可维护性
- 一个路由下的组件属性和来源一致，代码已读和容易理解

#### css模块化
- 可以使用CSS Modules
- 用less sass等预编译器，给组件最外层容器设置唯一的className，当前组件的所有样式包含在这个样式下面
- 全局的样式在入口文件导入
- 优先使用全局样式，可以用多个全局的样式组合出想要的样式

```jsx
    <div className="user-list">
      <div className="header"></div>
      <div className="list"></div>
    </div>
   
   .user-list{
     font-size: 14px;
     
     .list{
       padding: 10px;
     }
   } 
```

#### *为什么*
- 使用局部作用域的样式可以防止样式污染全局样式
- 使用全局样式可以减少局部样式，从而减小应用的体积
- 使用全局样式可以在有需要的时候统一调整样式

#### 按需使用全局store
- store只保存组件间需要共享的数据
- 展示组件的属性通过父组件传递

```js
// 这个方法请求接口返回的数据只是当前组件使用，
// 直接通过then返回就好了，不用保存在store
      async test () {
        const data = await this.props.actions.getTodoList()
        console.log(data) 
      }
```

#### *为什么*
- 减小store的体积，如果需要持久化store，这将很有用
- 太多的数据会让store变得难以维护和阅读
- 让组件的数据来源变得单一，使组件的状态容易理解

#### 使用扁平化的属性
#### 列表的key
#### 无状态组件
#### 用装饰器代替mixins
#### 不要使用refs
#### 推荐的jsx文件结构