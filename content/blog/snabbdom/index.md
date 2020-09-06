---
title: Snabbdom 源码
date: "2020-08-31 23:18:49"
description: 分享
---

[官方](https://github.com/snabbdom/snabbdom)是这么介绍 Snabbdom 的：
> A virtual DOM library with focus on simplicity, modularity, powerful features and performance.

一个具有简单性、模块化、强大的功能和性能的虚拟 DOM 库。那什么是虚拟 DOM，以及为什么要使用虚拟 DOM 呢？

### Virtual DOM

> 由普通的 JS 对象来描述 DOM 对象，是对真实 DOM 的一种抽象，因为不是真的 DOM 对象，所以叫 Virtual DOM。

真实的 DOM
```js
const ele = document.querySelector('.logged-in')
let s = ''
for (const key in ele) {
  s += key + ',
}
console.log(s)
```
虚拟 DOM
```js
{
  el: 'div,
  data: {},
  text: 'Hello World',
  children: [],
  key: undefined,
}
```

**为什么要使用 Virtual DOM**

- 手动操作 DOM 比较麻烦，虽然有 jQuery 这样的库简化DOM操作，但没有解决大批量操作 DOM 的性能问题（虽然可以使用一些技巧，比如试用 DocumentFragment 将多次 DOM 操作进行一次挂载，但是这样随着项目体积的增加，维护起来非常痛苦）。
- 为了简化 DOM 操作出现了各种 MVVM 框架，解决了视图和状态同步问题。
- 简化视图的操作可以使用模板引擎，但是模板引擎没有解决跟踪状态变化的问题，于是 Virtual DOM 出现了。
- [Virtual DOM](https://github.com/Matt-Esch/virtual-dom) 的好处是当状态改变时不需要立即更新 DOM，只需要创建一个虚拟树来描述 DOM，Virtual DOM 内部来更有效的更新 DOM (diff)。
  - 虚拟 DOM 可以维护程序的状态，跟踪上一次的状态。
  - 通过比较前后两次状态的差异更新真实 DOM。

**Virtual DOM 的作用**

- 维护视图和状态的关系
- 复杂视图情况下提升渲染性能
- 出来渲染真实 DOM 外，还可以实现 SSR(Nuxt.js/Next.js)、原生应用(Weex/React Native)、小程序(mpvue/uni-app)等

> 举个操作真实 DOM 和虚拟 DOM 差异的例子。

**Virtual DOM 需要解决的问题**

- 高效的 diff 算法，即两个 Virtual DOM 的比较
- 只更新需要更新的 DOM 节点
- 数据变化检测，批量 DOM 读写操作等等

### Snabbdom 基本使用

Snabbdom 的引入方式(ES6 模块和 Common JS 的差异)，同时 v1 版本有所区别。

snabbdom 主要就几个函数

`init` 是一个高阶函数，返回 `patch` 函数。

模块：官方提供了6个模块，attitudes props class dataset eventlisteners style

### Snabbdom 源码分析

**如何学习源码：**

- 先宏观了解
- 带着目标看源码
- 看源码的过程要不求甚解
- 调试
- 参考资料（比如网上其他源码分析的文章）

**Snabbdom 的核心：**

- 使用`h`函数创建 JavaScript 对象（VNode）描述真实 DOM
- `init`设置模块，创建`patch`
- `patch`比较新旧两个 VNode
- 把变化的内容更新到真实的 DOM 树上

#### init 函数

#### patch 函数

比较新旧两个 VDOM 树并更新

#### h 函数

返回一个 vnode 对象。

Vue 中就有 h 函数
```js
new Vue({
  render: h => h(App)
}).$mount('#app')
```

**函数的重载：** 
- 参数个数或类型不同的函数
- JavaScript 中没有重载的概念
- TypeScript 中有重载，不过重载的实现还是通过代码调整参数

示例：
```js
function add(a, b) {
  return a + b
}

function add(a, b, c) {
  return a + b + c
}

add(1 + 2)
add(1 + 2 + 3)

```


#### patch 函数

打补丁，把新节点中变化的内容渲染到真实 DOM，最后返回新节点作为下一次处理的旧节点。

- 对比新旧 VNode 是否是相同的节点（根据节点的 key 和 sel 进行判断；判断两个数的时间复杂度一般是O(n^3), 通过这连个条件可以降低复杂度为O(n)）
- 如果不是相同节点，则直接替换
- 如果是相同节点，再判断新的 VNode 是否有 text，如果有并且和 oldVNode 的 text 不同，直接更新文本
- 如果新的 VNode 有 children，判断子节点是否有变化，判断子节点的过程使用的就是 diff 算法
- diff 过程只进行同层级比较

[React Diff 算法]()

#### createElm 函数

把 vnode 转化为 DOM 元素。

createElm
  - 触发用户钩子函数 init
  - 把 vnode 转换为 DOM 对象，存储到 vnode.elm 中（没有渲染到页面）
    - sel 是 !
      - 创建注释节点
    - sel 不为空
      - 创建对应的 DOM 对象
      - 触发模块的的狗子函数 create
      - 创建所有子节点对用的 DOM 对象
      - 触发用户的的钩子函数 create
      - 如果 vnode 有 insert 钩子函数，追加到队列
    - sel 为空
      - 创建文本节点
  - 返回 vnode
  