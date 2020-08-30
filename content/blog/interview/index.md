---
title: 重构
date: "2020-08-24 23:29:15"
description: "准备总结"
---

### 重构的原则

**重构（名词）：**对软件内部结构的一种调整，目的是在不改变软件可观察行为的前提下，提高其可理解性，降低其修改成本。

**重构（动词）：**使用一系列重构方法，在不改变软件可观察行为的前提下，调整其结构。

重构的关键在于运用大量微小且保持软件行为的步骤，一步步达成大规模的修改。可观察行为的意思是经过重构之后的代码应该与重构之前大致一样。重构是为了让代码“更容易理解，更容易修改”，但可能是程序运行的更慢或者更快，与之相似的是**性能优化**可以是程序运行的更快当时代码可能会更难理解。这两者之间需要更好的平衡。

为何重构？<br>***重构改进软件的设计***。如果没有重构，程序内部的代码（或者叫架构）会变得腐败变质，就像裹脚布又臭又长，变得更难理解以及维护。这是因为人们可能会因为需求的紧急导致在没有完全理解程序的整体设计的情况下，使得代码失去了原有的结构。经常性重构有助于代码维护自己该有的形态。改进设计的一个重要方向就是消除重复代码。***重构是软件更容易理解***。编写代码是告诉计算机做什么事，而它的响应事按照我的指示精确行动。所做的事情就是填补“我想要它做什么”和“我告诉它做什么”之间的缝隙。编程的核心就是在于“准确说出我想要”。当然更重要的是阅读我们代码不仅仅是计算机，而是接手我们代码的同行，这才是最重要的。我觉得如何使其他人更好的理解你的代码，需要要个技术文档，在接到需求时，需要先写一份技术文档，主要应该包括需求的理解，程序的设计实现，流程图之类的。编写代码可能只会占35%的时间，就像做题的时候需要打草稿理清解题思路，盖楼前需要有建筑设计图。重构还能帮助找到bug，提高编程速度。

### 第一组重构

#### 1.1 提炼函数 <-> 内联函数

**提炼函数**

提炼函数。将一些常用的代码放进独立的函数，从复用的角度来看，只要被用过不止一次的代码，就应该单独放进一个函数。最合理的观点是“将意图与实现分开”：如果你需要时间浏览一段代码才能弄秦它到底在干什么，那么就应该将其提炼到一个函数，并根据它所做的事为其命名。
```js
// ------------- before ---------------------
function printOwing(invoice) {
  console.log('*************************');
  console.log('***** Customer Owes *****');
  console.log('*************************');

  // calculate outstanding
  let outstanding = 0;
  for (const o of invoice.orders) {
    outstanding += o.amount;
  }

  // print details
  console.log(`name: ${invoice.customer}`);
  console.log(`amount: ${outstanding}`);
}

// --------------- after ---------------------
function printOwing(invoice) {
  printBanner();
  const outstanding = calculateOutstanding(invoice);
  printDetails(outstanding);

  // 提炼函数
  // 无局部变量
  function printBanner() {
    console.log('*************************');
    console.log('***** Customer Owes *****');
    console.log('*************************');
  }
  // 有局部变量
  function printDetails(outstanding) {
    console.log(`name: ${invoice.customer}`);
    console.log(`amount: ${outstanding}`);
  }
}

// 如果有些编程语言不支持嵌套函数，则将函数提升到顶层作用域
// 对局部变量再赋值
function calculateOutstanding(invoice) {
  // 修改变量名易于理解
  let result = 0;
  for (const o of invoice.orders) {
    result += o.amount;
  }
  return result;
}
```

**内联函数**

当有些函数内部的代码和函数名称同样清晰易读，那就可以去掉这个函数直接使用函数内的代码。还有若函数组织不合理(复杂函数)，可以将它们都内联到一个大的函数里，再以自己喜欢的方式提炼出小函数。
重点在于始终小步前进。

```js
// ------------- before ----------------------
function rating(aDriver) {
  return moreThanFiveLateDeliveries(aDriver) ? 2 : 1;
}

function moreThanFiveLateDeliveries(dvr) {
  return dvr.numberOfLateDeliveries > 5;
}
// ------------- after -----------------------
function rating(aDriver) {
  return aDriver.numberOfLateDeliveries > 5 ? 2 : 1;
}
```

#### 1.2 提炼变量 <-> 内联变量

**提炼变量**

表达式有可能非常复杂难以阅读，局部变量可以将逻辑进行分解使得更好的阅读。在使用提炼变量的时候意味着需要将表达式命名，需要考虑这个名字所处的上下文。

```js
// ------------- before ----------------------
function price(order) {
  // price is base price - quantity discount + shipping
  return order.quantity * order.itemPrice - 
    Math.max(0, order.quantity - 500) * order.itemPrice * 0.05 +
    Math.min(order.quantity * order.itemPrice * 0.1, 100);
}
// ------------- after -----------------------
function price(order) {
  // 底价
  const basePrice = order.quantity * order.itemPrice;
  // 批发折扣
  const quantityDiscount = Math.max(0, order.quantity - 500) * order.itemPrice * 0.05;
  // 运费
  const shipping = Math.min(basePrice * 0.1, 100);
  return basePrice - quantityDiscount + shipping;
}
```

**内联变量**

当变量的名字并不比表达式本身更具表现力，这时候就是和用内联的方法消除变量。

#### 1.3 改变函数声明