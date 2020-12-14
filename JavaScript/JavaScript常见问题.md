# 1 Node与浏览器中JavaScript的区别

1. **全局环境下的this指向**
  - 在node中this指向global
  - 浏览器中this指向window

2. **js引擎**
  - 在浏览器中不同的浏览器厂商提供了不同的浏览器内核
  - Node对一些特殊用例进行了优化，提供了替代的API，使得V8引擎在非浏览器环境下运行的更好，例如，在服务器环境中，处理二进制数据通常是必不可少的，但是Javascript对此支持不足，因此，V8.Node增加了Buffer类，方便高效的处理二进制数据。因此，Node不仅仅简单的使用了V8，还对其进行了优化，使其在各环境下更加给力

3. **DOM操作**
  - 浏览器中的js大多数情况下是在直接或间接的操作DOM，因为浏览器中的代码主要是在表现层工作
  - node是一门服务器技术，没有前台页面，所以我们不会在node中操作DOM

4. **I/O读写**
  - 浏览器打开本地图片非常麻烦
  - nodejs提供了比较方便的组件      

5. **模块加载**
  - 在nodejs中提供了CMD的模块加载的API，node还提供了npm种种包管理工具，能更有效管理引用的库
  - 浏览器中有ES6的补充



# 2 CommonJS和ES6 Module的区别

- 因为CommonJS的require语法是**同步**的，所以就导致了CommonJS模块规范只适合用在**服务端**，而**ES6模块**无论是在浏览器端还是服务端都是可以使用的，但是在服务端中，还需要遵循一些特殊的规则才能使用；
- **CommonJS** 模块输出的是一个值的<font color=red>**拷贝**</font>，而**ES6 模块**输出的是值的<font color=red>**引用**</font>；
- **CommonJS** 模块是<font color=red>**运行时加载**</font>，而**ES6模块**是<font color=red>**编译时输出接口**</font>，使得对JS的模块进行静态分析成为了可能；
- 因为两个模块加载机制的不同，所以在对待循环加载的时候，它们会有不同的表现。**CommonJS**遇到循环依赖的时候，只会**输出已经执行的部分，后续的输出或者变化，是不会影响已经输出的变量**。而**ES6模块**相反，使用`import`加载一个变量，**变量不会被缓存，真正取值的时候就能取到最终的值**；

- 关于模块顶层的`this`指向问题，在**CommonJS**顶层，`this`指向当前模块；而在**ES6**模块中，`this`指向`undefined`；
- 关于两个模块互相引用的问题，在**ES6模块**当中，是支持加载**CommonJS**模块的。但是反过来，**CommonJS**并不能`require`**ES6模块**，在NodeJS中，两种模块方案是分开处理的。



# 3 实现(5).add(5).minus(1)

``` js
Number.prototype.add = function(number) {
    if(typeof number !== 'number') throw new Error('input a number please');
    return this.valueOf() + number;
}
Number.prototype.minus = function(number) {
    if(typeof number !== 'number') throw new Error('input a number please');
   	return this.valueOf() - number;
}
console.log((5).add(5).minus(1))	// 9
```



# 4 JavaScript类型判断的几种方法

1. **typeof操作符**

2. **0instanceof运算符**

3. **Object.prototype.toString** 

   

# 5 for-in和for-of的区别

**interable** 

- 解决for-in遍历索引（属性名）的问题
- 解决了map、set无法用下标遍历的问题

**for-in**

遍历的是对象的属性（属性需要 **`enumerable`**,才能被读取到），而不是数组的索引。`for-in` 循环只遍历可枚举属性。一般常用来遍历对象，包括**非整数类型的名称**和**继承的那些原型链上面的属性**也能被遍历。像 Array和 Object使用内置构造函数所创建的对象都会继承自Object.prototype和String.prototype的不可枚举属性就不能遍历了。

**for-of**

`for-of`语句在可迭代对象（包括 Array，Map，Set，String，TypedArray，arguments 对象等等）上创建一个迭代循环，调用自定义迭代钩子，并为每个不同属性的值执行语句。只要是一个iterable的对象,就可以通过`for-of`来迭代.。



# 6 JS检测属性位于对象本身还是来自于其原型链

### 6.1 in操作符

in操作符会在通过对象能够访问给定属性时返回true，无论该属性存在于对象本身还是其原型链上。

### 6.2 obj.hasOwnProperty(prop)

`hasOwnProperty()` 方法会返回一个布尔值，指示对象**自身**属性中是否具有指定的属性。

### 6.3 两者结合判断属性位于对象本身还是来自于其原型链

``` javascript
function propertyFormPrototype(obj, prop) {
    return !obj.hasOwnProperty(prop) && prop in obj
}
```



# 7 requestAnimationFrame的优点

- ##### requestAnimationFrame 会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率，一般来说，这个频率为每秒60帧。

- ##### 在隐藏或不可见的元素中，requestAnimationFrame将不会进行重绘或回流，这当然就意味着更少的的cpu，gpu和内存使用量。

