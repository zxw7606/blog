---
title: js prototype
date: 2019-10-27 19:42:51
tags:	
	- javascript
---



# 类的声明

```js
function Person() {
  // prototype
}
var p  = new Person() // new 声明为对象，否则为函数
// p.__proto__ = Person.prototype(p 的原型为 Person.prototype)
// Person.prototype 继承自 Object.protoype
// Person.prototype.__proto__ = Object.protoype
// 所以p的属性继承 Array.prototype，Object.protoype
```



{% asset_img 01.jpg  结构图1 %}



总结:

p 对象的原型为Person.prototype,

Person.prototype的原型为Object.prototype

补充: 

Person.prototype 中含有construct 属性,指向Person(Function)




# 判断变量是否存在

```js
var a = {x : undefined}
a.x !== undefined // false 
x in a //ture
```

