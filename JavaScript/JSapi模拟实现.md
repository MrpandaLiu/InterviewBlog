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
Functiopn.prototype.bind2 = function(context, ...args) {
  const that = this;
  return function F() {
    // 如果是使用new关键字
    if(this instanceof F) that.apply(this, args);
    else that.apply(context, args);
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

# 2 模拟实现call和apply

``` js
Function.prototype.call2 = function(context, ...args) {
  // 获取上下文对象
  context = context || window;
  // 将目标方法添加到context对象中
  context.fn = this;
  // 执行方法
  const result = context.fn(args);
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
  const result = context.fn(...arr);
  delete context.fn;
  return result;
}

function fn(a, b){
  return a+b;
}

const a = {}
console.log(fn.apply2(a, [3, 4]));  // 7
```


# 3 模拟实现new

在使用new运算符调用构造函数时的执行过程：

1. 创建一个空对象 obj;
2. 将新创建的空对象的隐式原型指向其构造函数的显示原型。
3. 使用 call 改变 this 的指向
4. 如果无返回值或者返回一个非对象值，则将 obj 返回作为新对象；如果返回值是一个新对象的话那么直接直接返回该对象

``` js
function myNew() {
  const obj = new Object();
  const Constructor = [].shift.apply(arguments);
  obj.__proto__ = Constructor.prototype;
  const ret = Constructor.apply(obj, arguments);
  return typeof ret==="object" ? ret : obj;
}
```



# 4 模拟实现instanceof

``` javascript
function _instanceof(A, B) {
    var o = B.prototype;	// 取B的显示原型
    A = A.__proto__;	// 取A的隐式原型
    while(true) {
        if(A === null) return false;	// Object.prototype.__proto__ === null
        if(o === A) return true;
        A = A.__proto__;
    }
}
```



# 5 实现发布订阅模式EventEmitter

``` javascript
class EventEmitter {
    constructor() {
        // 事件对象
        this.events = {};
    }
    // 订阅事件的方法
    on(name, callback) {
        if(!this.events[name]) this.events[name] = [callback];
        else this.events[name].push(callback);
    }
    // 触发事件的方法
    emit(name, ...args) {
        this.events[name] &&
        	this.events[name].forEach((cb) => cb.apply(this, args));
    }
    // 移除订阅事件
    off(name, callback) {
        if(this.events[name]) {
            this.events[name] = this.events[name].filter((cb) => cb!=callback);
        }
    }
    // 只执行一次订阅事件，然后移除
    once(name, callback) {
        const fn = () => {
            callback();
            this.off(name, fn);
        }
        this.on(name, fn);
    }
}
```

