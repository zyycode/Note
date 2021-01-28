---
title: 面试
date: "2021-01-07 16:15:15"
description: "面试题"
---

# Vue

1. **v-show 和 v-if 的区别**

- v-show 通过 CSS display 控制显示和隐藏
- v-if 组件真正的渲染和销毁，而不是显示和隐藏
- 频繁切换显示状态用 v-show，否则用 v-if

2. **为何在 v-for 中使用 key**

- 必须使用 key， 且不能是 index 和 random
- diff 算法中通过 tag 和 key 来判断，是否是 sameNode
- 减少渲染次数，提升渲染性能

3. **描述 Vue 组件生命周期（父子组件）**

- 单组件生命周期图
- 父子组件生命周期关系

渲染 -> 更新 -> 销毁

### 组件的渲染与更新。

**初次渲染过程**

- 解析模板为 render 函数（或在开发环境已经完成，vue-loader）
- 触发响应式，监听 data 属性 getter setter
- 执行 render 函数，生成 vnode ， patch(elem, vnode)

**更新过程**

- 修改 data ，触发 setter （此前在 getter 中已监听）
- 重新执行 render 函数，生成 newVnode
- patch(vnode, newVnode)

**异步渲染**

- \$nextTick
- 汇总 data 的修改，一次性更新视图
- 减少 DOM 操作次数，提高性能

# React

### event 事件

1. event 是 SyntheticEvent （组合事件），模拟出来 DOM 事件所有能力
2. event.nativeEvent 是原生事件对象
3. 所有的事件，都是挂载到 document 上
4. 和 DOM 事件不一样，和 Vue 事件也不是一样

### React 为什么使用合成事件

- 更好的兼容性和跨平台
- 挂载到 document ，减少内存消耗，避免频繁解绑
- 方便事件的统一管理（事务机制）

### React 事务机制

```js
/**
 * <pre>
 *                                 wrappers (创建时注入)
 *                                      +        +
 *                                      |        |
 *                    +-----------------|--------|--------------+
 *                    |                 v        |              |
 *                    |      +---------------+   |              |
 *                    |   +--|    wrapper1   |---|----+         |
 *                    |   |  +---------------+   v    |         |
 *                    |   |          +-------------+  |         |
 *                    |   |     +----|   wrapper2  |--------+   |
 *                    |   |     |    +-------------+  |     |   |
 *                    |   |     |                     |     |   |
 *                    |   v     v                     v     v   | wrapper
 *                    | +---+ +---+   +---------+   +---+ +---+ | invariants
 * perform(任何方法)   | |   | |   |   |         |   |   | |   | | maintained
 * +----------------->|-|---|-|---|-->| 任何方法 |---|---|-|---|-|-------->
 *                    | |   | |   |   |         |   |   | |   | |
 *                    | |   | |   |   |         |   |   | |   | |
 *                    | |   | |   |   |         |   |   | |   | |
 *                    | +---+ +---+   +---------+   +---+ +---+ |
 *                    |  initialize                    close    |
 *                    +-----------------------------------------+
 * </pre>
 */
```

### 组件使用

- props 传递数据
- props 传递函数
- props 类型检查

### setState

- 不可变值
- 可能是异步更新
- 可能会被合并

直接使用是异步的， 在自定义的 DOM 事件和 setTImeout 里使用时同步的。

### React 组件渲染和更新

1. 组件渲染过程

- props state
- render() 生成 vnode
- patch(elem, vnode)

2. 组件更新过程

- setState(newState) -> dirtyComponents （可能是自组件）
- render() 生成 newVnode
- patch(vnode, newVnode)

patch 被拆分为两个阶段：

- reconciliation 阶段 - 执行 diff 算法，纯 JS 计算
- commit 阶段 - 将 diff 结果渲染到 DOM

可能会存在的性能问题

- JS 是单线程，且和 DOM 渲染共用一个线程
- 当组件足够复杂，组件计算和渲染都压力大
- 同时再有 DOM 操作需求（动画，鼠标拖拽等），将卡顿

解决方案 fiber

- 将 reconiliation 阶段进行任务拆分（commit 无法拆分）
- DOM 需要渲染时暂停，空闲时恢复
- window.requestIdleCallback 判断是否渲染

### React 性能优化

- 渲染列表加 key
- 自定义事件、DOM 事件及时销毁
- 合理使用异步组件
- 减少函数 bind this 次数
- 合理使用 SCU PureComponent 和 memo
- 使用 Immutable.js
- webpack 层面优化
- 前端通用性能优化，如图片懒加载
- 使用 SSR

### React 和 Vue 的区别

- 都支持组件化
- 都是数据驱动视图
- 都是使用 vdom 操作 DOM

- React 使用 JSX 拥抱 JS， Vue 使用末班拥抱 html
- React 函数式编程，Vue 声明式编程
- React 更更多需要自力更生，Vue 把想要的都给你

### webpack 高级配置

- 多入口
- 抽离 css 文件
- 抽离公共代码
- 懒加载
- 处理 JSX @babel/preset-jsx
- 处理 Vue vue-loader

### module chunk bundle 的区别

- module - 各个源码文件，webpack 中一切皆模块
- chunk - 多模块合并成的，如 entry import() splitChunk
- bundle - 最终输出文件

### webpack 性能优化

1. 优化打包构建速度 - 开发体验和效率

- 优化 babel-loader

```js
{
  test: /\.js$/,
  use: ['babel-loader?cacheDirectory'], // 开启缓存
  include: path.resolve(__dirname, 'src'), // 明确范围
  // // 排除范围，include 和 exclude 两者选一
  // exclude: path.resolve(__dirname, 'node_modules')
}
```

- IngorePlugin 避免引入无用模块

```js
// 手动引入中文语言包
// import 'moment/locale/zh-cn'
new webpack.IngorePlugin(/\.\/locale/, /moment/)
```

- noParse 避免重复打包

```js
module.exports = {
  module: {
    // 单独完整的 react.min.js 文件就没有采用模块化
    // 忽略 react.min.js 文件的递归解析处理
    noParse: [/react\.min\.js$/],
  },
}
```

- happyPack 多进程打包
- ParallelUglifyPlugin 多进程压缩 JS

```js
const HappyPack = require("happypack")
const ParalleUglifyPlugin = require('webpack-parallel-uglify-plugin')

module.expores = {
  module: {
    rules: [
      {
        test: /\.js$/,
        // 将对 .js 文件的处理交给 id 为 babel 的 HappyPack 的实例
        use: ["happypack/loader?id=babel"],
        include: path.resolve(__dirname, "src"),
      },
    ],
  },
  plugins: {
    new HappyPack({
      // 用唯一的标识符 id 来代表当前的 HappyPack 是用来处理一类特定的文件
      id: 'babel',
      loaders: ['babel-loader?cacheDirectory']
    }),

    // 使用 ParalleUglifyPlugin 并行压缩输出的 JS 代码
    new ParalleUglifyPlugin({
      // 传递给 UglifyJS 参数
      // （还是使用 UglifyJS 压缩，只不过帮助开启了多进程）
      UglifyJS: {
        output: {
          beautify: false, // 最紧凑输出
          comments: false, // 删除所有的注释
        },
        compress: {
          // 删除所有的 console 语句，可以兼容 ie 浏览器
          drop_console: true,
          // 内嵌定义了但是只用到一次的变量
          collapse_vers: true,
          // 提取出出现多次但是没有定义成变量去引用的静态值
          reduce_vars: true,
        }
      }
    })
  }
}
```

- 自动刷新
- 热更新
- DllPlugin 动态链接库插件
  - webpack 已内置 DLLPlugin 支持
  - DllPlugin - 打包出 dll 文件
  - DllReferencePlugin - 使用 dll 文件

2. 优化产出代码 - 产品性能

- 小图片 base64 编码
- bundle 加 hash
- 懒加载 - 异步加载
- 提取公共代码
- IngorePlugin
- 使用 CDN 加速
- 使用 production
- Scope Hosting

### 项目流程

- 需求分析
- 技术方案设计
- 开发
- 联调
- QA 测试
- 上线

### 需求分析

- 了解背景
- 质疑需求是否合理
- 需求是否闭环
- 开发难度如何
- 是否需要其他支持
- 不要急于给排期

### 技术方案设计

- 求简，不过度设计
- 产出文档
- 找准设计重点
- 组内评审
- 和 RD CRD 沟通
- 发出会议结论

### 开发

- 如何返回排期（预留时间）
- 符合开发规范
- 写出开发文档
- 及时单元测试
- Mock API
- Code Review

### 联调

- 和 RD CRD 技术联调
- 让 UE 确定视觉效果
- 让 PM 确定产品功能

PM 加需求怎么办？

- 不能决绝，走需求变更流程即可
- 如果公司有规定，则按规定走
- 否则，发起项目组合 leader 的评审，重新评估排期

## React Hooks

### 函数组件的特定

- 没有组件实例
- 没有生命周期
- 没有 state 和 setState ，只能接受 props

### class 组件的问题

- 大型组难拆分和重构，很难测试（即 class 不易拆分）
- 相同业务逻辑，分散到各个方法中，逻辑混乱（请求数据 mounted updated 重复）
- 复用逻辑的复杂，如 Mixins、HOC、Render Prop

### React 组件更易用函数表达

- React 提倡函数式编程，view = fn(props)
- 函数更灵活，更易拆分，更易测试
- 但函数组件太简单，需要增强能力 - Hooks

### State Hook

让函数组件实现 state 和 setState

- 默认函数组件没有 state
- 函数组件是一个个纯函数，执行完即销毁，无法存储 state
- 需要 state Hook ，即把 state 功能 “钩” 到纯函数中

### Effect Hook

让函数组件模拟生命周期
让纯函数有了副作用
模拟 WillUnMounted 生命周期，但是不等于

- 默认函数组件没有生命周期
- 函数组件是一个纯函数，执行完即销毁，自己无法实现生命周期
- 使用 Effect Hook 把生命周期 “钩” 到纯函数中

### useMemo 和 useCallback

- useMemo 缓存数据
- useCallback 缓存函数

### 自定义 Hook

- 封装通用功能
- 开发和使用第三方 Hooks
- 自定义 Hook 带来了无限的扩展性，解耦代码

```jsx
import { useState, useEffect } from "react"
import axios from "axios"

// 封装 axios 发送网络请求的自定义 Hook
function useAxios(url) {
  const [loading, setLoading] = useState(false)
  const [data, setData] = useState()
  const [error, setError] = useState()

  useEffect(() => {
    // 利用 Axios 发送网络请求
    setLoading(true)
    axios
      .get(url)
      .then(res => setData(res))
      .catch(err => setError(err))
      .finally(() => setLoading(false))
  }, [])

  return [loading, data, error]
}

export default useAxios
```

### Hooks 使用规范

- 只能用于 React 函数组件和自定义 Hook 中，其他地方不可以
- 只能用于顶层代码，不能在循环、判断中使用 Hooks
- eslint 插件 eslint-plugin-react-hooks

### Mixins 问题

- 变量作用域来源不清
- 属性重名
- Mixins 引入过多会导致顺序冲突

### Hoc 问题

- 组件层级嵌套过多，不易渲染，不易调试
- HOC 会劫持 props ，必须严格遵守规范，容易出疏漏

### Render Prop 问题

- 学习成本高，不易理解
- 只能传递纯函数，而默认情况下纯函数有限

### React Hooks 做组件逻辑复用的优点

- 完全符合 Hooks 原有规则，没有其他要求，易理解记忆
- 变量作用域明确
- 不会产生组件嵌套

```jsx
import { useState, useEffect } from "react"

function useMousePositon() {
  const [x, setX] = useState(0)
  const [y, setY] = useState(0)

  useEffect(() => {
    function removeMoveHandle(event) {
      setX(event.clientX)
      setY(event.clientY)
    }
    document.addEventListener("mousemove", removeMoveHandle)
    return () => document.removeEventListener("mousemove", removeMoveHandle)
  }, [])

  return [x, y]
}

export default useMousePositon
```

### React Hooks 注意事项

- useState 初始化值 ，只有第一次有效
- useEffect 内部不能修改 state（依赖为空时，即模拟 DidMount ，re-render 不会重新执行 effect 函数）
- useEffect 可能出现死循环（依赖中有引用类型）

### 为什么会有 React Hooks，解决了什么问题？

- 完善函数组件的能力，函数组件更适合 React 组件
- 组件逻辑复用，Hooks 表现更好
- class 组件正在变得费解，不易拆解，不易测试，逻辑混乱
  - DidMount 和 DidUpdate 中获取数据
  - DidMount 绑定事件，WillUnMount 解绑事件
  - 使用 Hooks ，相同的逻辑可分割到一个一个 useEffect 中

### 手写代码

```js
// -------------------------------------算法题----------------------------------

/**
 * LeetCode 53
 * 给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
 * 输入: [-2,1,-3,4,-1,2,1,-5,4]
 * 输出: 6
 * 解释: 连续子数组 [4,-1,2,1] 的和最大，为 6
 */
const maxSubArray = function(nums) {
  let sum = 0
  let max = nums[0]
  for (const n of nums) {
    if (sum > 0) {
      sum += n
    } else {
      sum = n
    }
    max = Math.max(max, sum)
  }
  return max
}

// -------------------------------------手写代码--------------------------------
// 节流和防抖
function debounce(fn, delay = 200) {
  let timer = null
  return function(...args) {
    if (timer) clearTimeout(timer)
    timer = setTimeout(() => {
      fn.apply(this, args)
    }, delay)
  }
}

// function throttle(fn, delay) {
//   let flag = true;
//   return function (...args) {
//     if (!timer) return;
//     flag = false;
//     setTimeout(() => {
//       fn.apply(this, args);
//       flag = true;
//     }, delay)
//   }
// }

// function throttle(fn, delay) {
//   let last = 0;
//   return function (...args) {
//     let now = Date.now();
//     if (now - last < delay) return;
//     last = now;
//     fn.apply(this, args);
//   }
// }

function throttle(fn, delay) {
  let timer = null
  let last = 0
  return function(...args) {
    if (now - last < delay && timer) {
      clearTimeout(timer)
      timer = setTimeout(() => {
        fn.apply(this, args)
      }, delay)
    } else {
      last = now
      fn.apply(this, args)
    }
  }
}

// 深拷贝

function cloneDeep(obj, map = new WeakMap()) {
  // 基本数据
  if (!obj instanceof Object) return obj
  if (obj instanceof Date) return new Date(obj)
  if (obj instanceof RegExp) return new RegExp(obj.source, obj.flags)

  if (map.get(obj)) return map.get(obj)
  if (obj instanceof Function) {
    return function() {
      return obj.apply(this, [...arguments])
    }
  }
  const res = new obj.constructor()
  //   const res = Array.isArray(obj) ? [] : {};
  obj instanceof Object && map.set(obj, res)
  if (obj instanceof Map) {
    obj.forEach((item, index) => {
      res.set(cloneDeep(index, map), cloneDeep(item, map))
    })
  }
  if (obj instanceof Set) {
    obj.forEach(item => {
      res.add(cloneDeep(item, map))
    })
  }
  Object.keys(obj).forEach(key => {
    if (obj[key] instanceof Object) {
      res[key] = cloneDeep(obj[key], map)
    } else {
      res[key] = obj[key]
    }
  })
  return res
}

// 浅拷贝
function shallowClone(target) {
  if (typeof target === "object" && target !== null) {
    const cloneTarget = Array.isArray(target) ? [] : {}
    for (const prop in target) {
      if (target.hasOwnProperty(prop)) {
        cloneTarget[prop] = target[prop]
      }
    }
  } else {
    return target
  }
}

// Write an emitter class:
/**
  emitter = new Emitter ();
  // 1. Support subscribing to events.
  sub = emitter.subscribe('event_name', callback);
  sub2 = emitter.subscribe('event_name', callback2);
  // 2. Support emitting events.
  // This particular example should lead to the `callback` above being invoked with `foo` and `bar` as parameters.
  emitter.emit('event_name', foo, bar);
  // 3. Support unsubscribing existing subscriptions by releasing them.
  sub.release(); // `sub` is the reference returned by `subscribe` above
*/
class EventEmitter {
  constructor() {
    this.events = {}
  }

  on(name, cb) {
    if (this.events[name]) {
      this.events[name].push(cb)
    } else {
      this.events[name] = [cb]
    }
  }

  emit(name) {
    if (this.events[name]) {
      const tasks = this.events[name].slice()
      for (let fn of tasks) {
        fn.apply(this, [...arguments[1]])
      }
    }
  }

  off(name, cb) {
    const tasks = this.events[name]
    if (tasks) {
      const index = tasks.findIndex(fn => fn === cb)
      if (index > -1) {
        this.events[name].splice(index, 1)
      }
    }
  }

  once(name, cb) {
    const that = this
    function fn(...args) {
      cb.call(null, ...args)
      that.off(name, cb)
    }
    this.on(name, fn)
  }
}

// 批量请求函数

// multiRequest(urls,maxNum)

// 1. 批量请求函数，最大并发数maxNum。
// 2. 每当有一个请求返回，就留下一个空位，可以增加新的请求。
// 2. 所有请求完成后，结果按照urls里面的顺序依次打出。

function multiRequest(urls, maxNum) {
  const len = urls.length
  const result = new Array(len).fill(false)
  let count = 0

  return new Promise((resolve, reject) => {
    while (count < maxNum) {
      next()
    }
    function next() {
      let current = count++
      if (current >= len) {
        !result.includes(false) && resolve(result)
        return
      }
      const url = urls[current]
      fetch(url)
        .then(res => {
          result[current] = res
          if (current < len) {
            next()
          }
        })
        .catch(err => {
          result[current] = err
          if (current < len) {
            next()
          }
        })
    }
  })
}

// JS实现一个带并发限制的异步调度器Scheduler，保证同时运行的任务最多有两个。完善代码中Scheduler类，使得以下程序能正确输出

class Scheduler {
  constructor(maxCount = 2) {
    this.maxCount = maxCount
    this.quene = []
    this.task = []
  }
  add(promiseCreator) {
    this.quene.push(promiseCreator)
    return this.run()
  }

  run() {
    if (this.task.length < this.maxCount && this.quene.length) {
      const task = this.quene.shift()
      const promise = task().then(() => {
        this.task.splice(this.task.indexOf(promise), 1)
      })
      this.task.push(promise)
      return promise
    } else {
      return Promise.race(this.task).then(() => this.run())
    }
  }
}

const timeout = time =>
  new Promise(resolve => {
    setTimeout(resolve, time)
  })

const scheduler = new Scheduler()
const addTask = (time, order) => {
  scheduler.add(() => timeout(time)).then(() => console.log(order))
}

addTask(1000, "1")
addTask(500, "2")
addTask(300, "3")
addTask(400, "4")
// output: 2 3 1 4

// 一开始，1、2两个任务进入队列
// 500ms时，2完成，输出2，任务3进队
// 800ms时，3完成，输出3，任务4进队
// 1000ms时，1完成，输出1
// 1200ms时，4完成，输出4

// 手写Promise.all
// https://zhuanlan.zhihu.com/p/41502945

Promise.myAll = function(promises) {
  let count = 0
  let len = promises.length
  let res = []
  return new Promise((resolve, reject) => {
    for (const p of promises) {
      Promise.resolve(p)
        .then(data => {
          res[count++] = data
          if (len === count) {
            resolve(res)
          }
        })
        .catch(err => {
          reject(err)
        })
    }
  })
}

var promise1 = Promise.resolve(3)
var promise2 = 42
var promise3 = new Promise(function(resolve, reject) {
  setTimeout(resolve, 100, "foo")
})

Promise.myAll([promise1, promise2, promise3]).then(function(values) {
  console.log(values)
})

// -------------------------------------面试题----------------------------------

// koa 基本API是如何的，简述下洋葱圈模型，请实现一个处理 Middleware 的函数
// type Middleware = (ctx: object, next: () => Promise<void>) => Promise<void>
// function processMiddleware(middlewares: Middleware[]): Promise<void>
const middlewares = [
  async (ctx, next) => {
    console.log("m1 before")
    await next()
    console.log("m1 after")
  },
  async (ctx, next) => {
    console.log("m2 before")
    await next()
    console.log("m2 after")
  },
]

function compose(middlewares) {
  return function(context, next) {
    dispatch(0)
    function dispatch(i) {
      let fn = middlewares[i]
      if (i === middlewares.length) fn = next
      if (!fn) return Promise.resolve()
      try {
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)))
      } catch (error) {
        return Promise.reject(error)
      }
    }
  }
}

// const processMiddleware = (middlewares) => compose(middlewares)();
// processMiddleware(middlewares).then(() => {
//   console.log('End');
// });
// m1 before
// m2 before
// m2 after
// m1 after
// End

Promise.resolve()
  .then(() => {
    console.log(1)
  })
  .then(() => {
    console.log(2)
  })
setTimeout(() => {
  console.log(3)
}, 0)
console.log(4)
// 正确输出 4 1 2 3

async function async1() {
  console.log("async1 start")
  await async2()
  console.log("async1 end")
}

async function async2() {
  new Promise(function(resolve) {
    console.log("promise1")
    resolve()
  }).then(function() {
    console.log("promise2")
  })
}

console.log("script start")

setTimeout(function() {
  console.log("setTimeout")
}, 0)

async1()

new Promise(function(resolve) {
  console.log("promise3")
  resolve()
}).then(function() {
  console.log("promise4")
})

console.log("script end")
// script start
// async1 start
// promise1
// promise3
// script end
// promise2
// async1 end
// promise4
// setTimeout

// 字符串大数相加

var addStrings = function(num1, num2) {
  let i = num1.length - 1
  let j = num2.length - 1

  const res = []
  let carry = 0
  while (i >= 0 || j >= 0) {
    const n1 = i >= 0 ? num1.charAt(i) - "0" : 0
    const n2 = j >= 0 ? num2.charAt(j) - "0" : 0
    const sum = n1 + n2 + carry
    res.unshift(sum % 10)
    carry = Math.floor(sum / 10)
    i--
    j--
  }
  if (carry === 1) res.unshift(carry)
  return res.join("")
}

// 求平方根

var mySqrt = function(x) {
  if (x == 0 || x == 1) {
    return x
  }
  let left = 1
  let right = x
  while (right - left > 1) {
    let mid = Math.floor((right + left) / 2)
    if (mid * mid > x) {
      right = mid
    } else {
      left = mid
    }
  }
  return left
}

// 最长公共子串

// 扁平化树结构求最大深度
function convert(list) {
  const res = []
  const map = list.reduce((res, v) => ((res[v.id] = v), res), {})

  for (const item of list) {
    if (item.parentId === 0) {
      res.push(item)
      continue
    }
    if (item.parentId in map) {
      const parent = map[item.parentId]
      parent.children = parent.children || []
      parent.children.push(item)
    }
  }
  return res
}

// 语义化版本号大小判断

var compareVersion = function(version1, version2) {
  const arr1 = version1.split(".").map(v => parseInt(v))
  const arr2 = version2.split(".").map(v => parseInt(v))
  const len = Math.max(arr1.length, arr2.length)
  while (arr1.length < len) arr1.push(0)
  while (arr2.length < len) arr2.push(0)
  for (let i = 0; i < len; i++) {
    if (arr1[i] > arr2[i]) return 1
    if (arr1[i] < arr2[i]) return -1
  }
  return 0
}

// 字符串消除类似于俄罗斯方块

// 六位随机数生成

// 项目中的Bff的主要工作，graphQL的原理
// React的优化方式
// 页面的访问量，pv大概是多少
// 页面埋点管理的相应数据
// Webpack的打包第三方文件
// webpack的优化方式

// 编程题：
//  两个大数的加法
//  数组扁平化

function flatten(arr) {
  const stack = [...arr]
  const res = []
  while (stack.length) {
    const first = stack.shift()
    if (Array.isArray(first)) {
      stack.unshift(...first)
    } else {
      res.push(first)
    }
  }
  return res
}

const JSONP = ({ url, param = {}, cbKey, cb }) => {
  param[cbKey] = cbKey
  const newParam = Object.keys(param)
    .map(key => `${key}=${param[key]}`)
    .join("&")
  const script = document.createElement("script")
  script.setAttribute("src", `${url}?${newParam}`)
  document.body.appendChild(script)
}

// ajax

/**
 * ajax 上传
 * @param {Object} opt
 * @property {string} opt.url 请求地址
 * @property {string} opt.type 请求方法
 * @property {string} opt.dataType 请求返回格式
 * @property {string} opt.async 是否异步
 * @property {number} opt.timeout 超时时间
 * @property {FormData} opt.data 请求内容
 */
function ajax(opt) {
  return new Promise((resolve, reject) => {
    if (!opt || typeof opt !== "object")
      return reject(new Error("ajax 配置丢失"))
    if (!opt.url) return reject(new Error("ajax 的 url 是必须的"))

    const url = opt.url,
      type = opt.type || "GET",
      dataType = opt.dataType || "json",
      async = typeof opt.async === "undefined" ? true : !!opt.async,
      timeout = opt.timeout || 30000

    const xhr = new XMLHttpRequest()
    xhr.timeout = timeout
    xhr.responseType = dataType
    // 默认 异步 true
    // 同步请求时，xhr.timeout 须为0，xhr.withCredentials 须为false，xhr.responseType须为""
    xhr.open(type, url, async)
    xhr.onload = function(e) {
      if (
        ((xhr.status >= 200 && xhr.status <= 300) || xhr.status === 304) &&
        xhr.readyState === 4
      ) {
        return resolve(xhr.response)
      }
    }

    xhr.ontimeout = function(e) {
      return reject(new Error("请求超时"))
    }

    xhr.onerror = function(e) {
      return reject(new Error(e))
    }

    xhr.onabort = function(e) {
      return reject(new Error("请求终止"))
    }

    xhr.onprogress = updateProgress
    xhr.upload.onprogress = updateProgress

    function updateProgress(e) {
      if (e.lengthComputable) {
        const completePercent = e.loaded / e.total
        console.log("进度：", completePercent)
      }
    }

    // 触发 xhr.onloadstart 开始计时
    try {
      xhr.send(opt.data)
    } catch (error) {
      return reject(new Error(error))
    }
  })
}

// --------------quickSort------------
function quickSort(array) {
  if (array.length <= 1) {
    return [...array]
  }
  let pivot = Math.floor(Math.random() * array.length)
  let left = []
  let right = []
  let middle = []
  for (let val of array) {
    if (val < array[pivot]) {
      left.push(val)
    } else if (val > array[pivot]) {
      right.push(val)
    } else {
      middle.push(val)
    }
  }
  return quickSort(left).concat(middle, quickSort(right))
}

// ---------------mergeSort----------------
function mergeSort(arr) {
  // 采用自上而下的递归方法
  var len = arr.length
  if (len < 2) {
    return arr
  }
  var middle = Math.floor(len / 2),
    left = arr.slice(0, middle),
    right = arr.slice(middle)
  return merge(mergeSort(left), mergeSort(right))
}
function merge(left, right) {
  var result = []
  while (left.length && right.length) {
    if (left[0] <= right[0]) {
      result.push(left.shift())
    } else {
      result.push(right.shift())
    }
  }
  while (left.length) result.push(left.shift())
  while (right.length) result.push(right.shift())
  return result
}
```
