JavaScript中没有类，但是可以从构造器中创建对象，同时也提供了new运算符，使得构造器看起来更像一个类。除了宿主提供的一些内置函数，大部分JavaScript函数都可以当作构造器使用。构造器的外表跟普通函数一模一样，它们的区别在于被调用的方式。**当用new运算符调用函数时，该函数总会返回一个对象，通常情况下，构造器里的this就指向返回的这个对象。**

# 1 call和apply

## 1.1 call和apply的区别

- apply接受两个参数，第一个参数指定了函数体内this对象的指向，第二个参数为一个带下标的集合，这个集合可以为数组，也可以为集合。

- call传入的参数数量不固定，跟apply相同的是，第一个参数也是代表函数体内的this指向，从第二个参数开始往后，每个参数被依次传入函数。

- JavaScript的参数在内部就是一个数组来表示

- 第一个参数为null，函数体内的this会指向默认的宿主对象。在浏览器则是window

## 1.2 call和apply的用途

**1)改变this的指向**

``` javascript
const obj1 = {
  name: "panda"
}

const obj2 = {
  name: "cat"
}

name = "window";

const getName = function() {
  console.log(this.name);
}

getName();  // window
getName.call(obj1); // panda
getName.call(obj2); // cat
```

**2)实现bind**

``` js
Function.prototype.bind2 = function(context, ...args)  {
  return () => {
    return this.apply(context, ...args);
  }
}

const a = {
  name: 'a'
};
const fn = () => {
  console.log(this.name)
};
fn.bind2(a)();
```

# 2 模拟实现call

``` js
Function.prototype.call2 = function(context, ...args) {
  // 获取上下文对象
  context = context || window;
  // 将目标方法添加到context对象中
  context.fn = this;
  // 执行方法
  const result = eval(`context.fn(${args})`);
  // 删除上下文中的方法
  delete context.fn;
  // 返回结果
  return result;
}

function fn(a, b){
  return a+b;
}

const a = {}
console.log(fn.call2(a, 3, 4)); // 7


Function.prototype.apply2 = function(context, arr) {
  context = context || window;
  context.fn = this;
  let result;
  if(!arr)  result = context.fn();
  else result = eval(`context.fn(${arr})`);
  delete context.fn;
  return result;
}

function fn(a, b){
  return a+b;
}

const a = {}
console.log(fn.apply2(a, [3, 4]));  // 7
```

