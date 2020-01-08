---
title: js继承
date: 2017-02-09 11:24:21
tags: [js继承]
categories: js
---
收藏几种js继承的实现方式
### 定义父类
```js
// 定义一个动物类
function Animal (name) {
  // 属性
  this.name = name || 'Animal';
  // 实例方法
  this.sleep = function(){
    console.log(this.name + '正在睡觉！');
  }
}
// 原型方法
Animal.prototype.eat = function(food) {
  console.log(this.name + '正在吃：' + food);
};
```

<!-- more -->

**1.原型链继承**

核心： 将父类的实例作为子类的原型
```js
function Cat(){ 
}
Cat.prototype = new Animal();
Cat.prototype.name = 'cat';

//　Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.eat('fish'));
console.log(cat.sleep());
console.log(cat instanceof Animal); //true 
console.log(cat instanceof Cat); //true
```
特点：
- 非常纯粹的继承关系，实例是子类的实例，也是父类的实例
- 父类新增原型方法/原型属性，子类都能访问到
- 简单，易于实现

缺点：
- 要想为子类新增属性和方法，必须要在new Animal()这样的语句之后执行，不能放到构造器中
- 无法实现多继承
- 来自原型对象的引用属性是所有实例共享的（详细请看附录代码： 示例1）
- 创建子类实例时，无法向父类构造函数传参

推荐指数：★★（3、4两大致命缺陷）

__2.构造继承__

核心：使用父类的构造函数来增强子类实例，等于是复制父类的实例属性给子类（没用到原型）
```js
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // false
console.log(cat instanceof Cat); // true
```
特点：
- 解决了1中，子类实例共享父类引用属性的问题
- 创建子类实例时，可以向父类传递参数
- 可以实现多继承（call多个父类对象）

缺点：
- 实例并不是父类的实例，只是子类的实例
- 只能继承父类的实例属性和方法，不能继承原型属性/方法
- 无法实现函数复用，每个子类都有父类实例函数的副本，影响性能

推荐指数：★★（缺点3）

**3.实例继承**

核心：为父类实例添加新特性，作为子类实例返回

```js
function Cat(name){
  var instance = new Animal();
  instance.name = name || 'Tom';
  return instance;
}

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // false
```
特点：
- 不限制调用方式，不管是new 子类()还是子类(),返回的对象具有相同的效果

缺点：
- 实例是父类的实例，不是子类的实例
- 不支持多继承

推荐指数：★★

**4.拷贝继承**
```js
function Cat(name){
  var animal = new Animal();
  for(var p in animal){
    Cat.prototype[p] = animal[p];
  }
  Cat.prototype.name = name || 'Tom';
}

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // false
console.log(cat instanceof Cat); // true
```
特点：
- 支持多继承

缺点：
- 效率较低，内存占用高（因为要拷贝父类的属性）
- 无法获取父类不可枚举的方法（不可枚举方法，不能使用for in 访问到）

推荐指数：★（缺点1）

**5.组合继承**

核心：通过调用父类构造，继承父类的属性并保留传参的优点，然后通过将父类实例作为子类原型，实现函数复用

```js
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
Cat.prototype = new Animal();

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // true
```
特点：
- 弥补了方式2的缺陷，可以继承实例属性/方法，也可以继承原型属性/方法
- 既是子类的实例，也是父类的实例
- 不存在引用属性共享问题
- 可传参
- 函数可复用

缺点：
- 调用了两次父类构造函数，生成了两份实例（子类实例将子类原型上的那份屏蔽了）

推荐指数：★★★★（仅仅多消耗了一点内存）

**6.寄生组合继承**

核心：通过寄生方式，砍掉父类的实例属性，这样，在调用两次父类的构造的时候，就不会初始化两次实例方法/属性，避免的组合继承的缺点

```js
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
(function(){
  // 创建一个没有实例方法的类
  var Super = function(){};
  Super.prototype = Animal.prototype;
  //将实例作为子类的原型
  Cat.prototype = new Super();
})();

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); //true
```
特点：
- 堪称完美
缺点：
- 实现较为复杂

推荐指数：★★★★（实现复杂，扣掉一颗星）