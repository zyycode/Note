---
title: JavaScript知识点（一）
date: 2017-12-02 19:40:40
---

# 变量类型及计算
## 题目
### 1. JS中用 `typeof` 得到那些数据类型
```javascript
typeof undefined     // undefined
typeof '123'         // string
typeof 123           // number
typeof true          // boolean
typeof {}            // object
typeof []            // object
typeof null          // object
typeof console.log() //function
```
> typeof 只能区分值类型

<!-- more -->

### 2. 何时使用 `==` 和 `===`
```javascript
if (obj.a == null) {
    /*
        这里相当于 obj.a === null || obj.a === undefined, 简写形式
        这是jQuery源码的推荐写法
        除此之外都是用 ===
    */
}

function (a, b) {
    if (a == null) {
        // 这里也可以使用 == ,但是 == 之前的变量必须要有定义
    }
}
```
> ''(空字符串), 0, null, undefined都可以转换为 false

### 3. JS中的内置函数--数据封装对象
```javascript
Object
Number
String
Boolean
Function
Array
Date
RegExp
Error
```
### 4. JS变量按存储方式区分为那些类型，并描述其特点
- 值类型
```js
var a = 100;
var b = a;
a = 200;
console.log(b) // 100
```
- 引用类型：对象、数组、函数（节省内存空间，以指针方式存储）
```js
var obj1 = {x: 20};
var obj2 = obj1;
obj1.x = 30;
console.log(obj2.x) // 30
```
### 5. 如何理解JSON
JSON只是JS的一个内置对象，Math也是
```js
JSON.stingify({a: 10, b: 20}) // 对象 -> 字符串
JSON.parse("{'a': 10, 'b': 20}") // 字符串 -> 对象
```

# 原型和原型链
## 知识点
- 构造函数
- 构造函数 - 扩展
- 原型规则和示例
- 原型链
- instanceof

### 构造函数
```js
function Foo(name, age) {
    this.name = name;
    this.age = age;
    this.class = 'class one';
    //return this  //默认有这一行
}
// 创建多个对象
var f1 = new Foo('zhansan', 12);
var f2 = new Foo('lise', 13);
```
### 构造函数的扩展
- `var a = {}` 其实是 `var a = new Object()` 的语法糖
- `var a = []` 其实是 `var a = new Array()` 的语法糖
- `function Foo() {...}` 其实是 `var Foo = new Funcion() {...}` 的语法糖
- 使用 `instanceof` 判断一个函数是否是一个变量的构造函数

### 原型规则和实例
- 所有的引用类型（对象、数组、函数），都具有对象特性，即可自由扩展属性（除了”null”以外
- 所有的引用类型（对象、数组、函数），都有一个proto(隐式原型)属性，属性值是一个普通对象
- 所有的函数，都有一个prototype(显示原型)属性，属性值是一个普通对象
- 所有的引用类型（对象、数组、函数），proto属性值指向它的构造函数的 `prototype` 属性值
- 当试图寻找一个对象（引用类型）的某个属性时，如果这个对象本身没有这个睡醒，那么会去它的proto（即它的构造函数的prototype）中去寻找

```js
// 所有的引用类型都有可自由扩展属性
var obj = {};
obj.a = 100;
var arr =[];
arr.a = 200;
function fn() {};
fn.a = 100;

// 所有的引用类型都有一个默认的隐式原型属性
console.log(obj._proto_);
console.log(arr._proto_);
console.log(fn._proto_);

// 所有的函数都有一个显示原型的属性
console.log(fn.prototype);


// 所有引用类型的_proto_属性值指向它的构造函数的prototype属性值
console.log(obj._proto_ === Object.prototype);
```
```js
// 构造函数
function Foo(name, age) {
    this.name = name;
}
Foo.prototype.alertName = function () {
    alert(this.name);
}

// 创建实例
var f = new Foo('zhangsan');
f.printName = function () {
    console.log(this.name);
}

// 测试
f.alertName();
f.printName();

// 循环对象自身的属性
var item;
for (item in f) {
    /*
        高级浏览器已经在 for in 中屏蔽了来自原型的属性
        但还是建议加上下面这个判断，保证程序的健壮性
    */
    if (f.hasOwnProperty(item)) {
        console.log(item);
    }
}

// 原型链
f.toString() // 要去 f._proto_._proto_ 中查找
```
### instanceof
用于判断**引用类型**属于哪个**构造函数**的方法
f `intanceof` Foo 的判断逻辑：
- `f.proto` 一层一层往上，能否对应到 `Foo.prototype`
- 再试着判断 `f instanceof Object`
- 同样在 `f.proto` 一层一层往上找，直到 `Object.prototype`

## 题目
### 1. 如何准确判断一个变量是数组类型
```js
var arr = [];
arr instanceof Array; // true
typeof arr; // Object, typeof 是无法判断是否为数组的
```
### 2. 写一个原型链继承的例子
```js
// 动物
function Animal() {
    this.eat = function () {
        console.log('Animal eat');
    }
}
// 狗
function Dog() {
    this.bark = function () {
        console.log('dog bark');
    }
}
Dog.prototype = new Animal();
// 哈士奇
var hashiqi = new Dog();
hashiqi.eat();
hashiqi.bark();
```
> **面试中千万不要上面的例子，应该写更加贴近实战的例子，上面这个例子只是用来理解。**

一个封装DOM查询的例子
```js
function Elem(id) {
    this.Elem = document.getElementById(id);
}
Elem.prototype.html = function (val) {
    var elem = this. Elem;
    if (val) {
        elem.innerHTML = val;
        return this
    } else {
        return elem.innerHTML
    }
}
Elem.prototype.on = function (type, fn) {
    var elem = this.Elem;
    elem.addEventListener(type, fn);
    return this
}
var div1 = new Elem('div1');
div.html('<p>hello</p>').on('click', function () {alert('clicked')}).html('<p>JavaScript</p>');
```
### 3. 描述 new 一个对象的过程
- 创建一个新对象
- this 指向这个新对象
- 执行代码，即对 this 进行赋值
- 返回 this

### 4. zepto(或其他框架)源码中如何使用原型链
- 阅读源码是高效提升技能的方式
- 但不能“埋头苦研”有技巧在其中，阅读之前在网上先搜索一下资料


