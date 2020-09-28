<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-07-29 20:34:58
 * @FilePath: \yunniubaoc:\Users\23163\Desktop\web\Blog\blog\JS继承的多种方式.md
 * @Description: add some description
--> 
# 1.原型链继承

``` js
function Parent() {
  this.name = 'panda';
}

Parent.prototype.getName = () => {
  console.log(this.name);
}

function Child() {}

Child.prototype = new Parent();
const child1 = new Child();

console.log(child1.getName());
```

问题：

1. 引用类型的属性被所有实例共享

``` js
function Parent() {
  this.names = ["panda","bear"];
}

function Child() {}

Child.prototype = new Parent();

const child1 = new Child();

child1.names.push("cat");

console.log(child1.names); // ["panda","bear","cat"]

const child2 = new Child();

console.log(child2.names);  // ["panda","bear","cat"]
```

2. 在创建Child的实例时，不能向Parent传参

# 2.借用构造函数（经典继承）

``` js
function Parent() {
  this.names = ["panda","bear"];
}

function Child() {
  Parent.call(this);
}

const child1 = new Child();

child1.names.push("cat");

console.log(child1.names); // ["panda","bear","cat"]

const child2 = new Child();

console.log(child2.names);  // ["panda","bear"]
```

优点：

1. 避免了引用类型的属性被所有实例共享

2. 可以在Child中向Parent传参

``` js
function Parent(name) {
  this.name = name;
}

function Child(name) {
  Parent.call(this, name);
}

const child1 = new Child("panda");

console.log(child1.name); // "panda"

const child2 = new Child("bear");

console.log(child2.name); // "bear"
```

缺点：

方法都在构造函数中定义，每次创建实例都会创建一遍方法

# 3.组合继承

原型链继承和经典双剑合璧

``` js
function Parent(name) {
  this.name = name;
  this.colors = ["red", "blue", "green"];
}

Parent.prototype.getName = () => {
  console.log(this.name);
}

function Child(name, age) {
  Parent.call(this, name);
  this.age = age;
}

Child.prototype = new Parent();

const child1 = new Child("畅喵", "20");

child1.colors.push("black");

console.log(child1.name); // 畅喵
console.log(child1.age);  // 20
console.log(child1.colors); // ["red", "blue", "green", "black"]

const child2 = new Child("堃煜喵", "20");

console.log(child2.name); // 堃煜喵
console.log(child2.age);  // 20
console.log(child2.color);  // ["red", "blue", "green"]
```

**优点**：融合原型链继承和构造函数的优点

**缺点**：会调用两次父构造函数

# 4.原型式继承

``` js
function createObj(o) {
  function F() {}
  F.prototype = o;
  return new F();
}
```
就是ES5 Object.create的模拟实现，将传入的对象作为创建的对象的原型

**缺点**：包含引用类型的属性值始终都会共享相应的值，和原型链继承一样。

# 5.寄生式继承

``` js
function createObj(o) {
  const clone = Object.create(o);
  clone.sayName = () => {
    console.log("hi");
  }
  return clone;
}
```
**缺点**：和借用构造函数模式一样，每次创建对象都会创建一遍方法

# 6.寄生组合式继承

``` js
function Parent(name) {
  this.name = name;
  this.colors = ["red", "blue", "green"];
}

Parent.prototype.getName = () => {
  console.log(this.name);
}

function Child(name, age) {
  Parent.call(this, name);
  this.age = age;
}

// 关键的三步
const F = function() {};

F.prototype = Parent.prototype;

Child.prototype = new F();

const child1 = new Child("panda", 20);

console.log(child1);
```

``` js
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}

function prototype(child, parent) {
  const prototype = object(parent.prototype);
  prototype.constructor = child;
  child.prototype = prototype;
}

prototype(Child, Parent);
```