# 1 vue内部运行机制

### 1. 1 初始化及mount

1. `new Vue()`后，vue 会执行一个`init()`的方法,这里的初始化包括生命周期、事件、props、data、methods、computed、watch,以及对所有data进行observer（用来实现**依赖收集**以及**数据响应式**）。

2. 初始化之后会执行`$mount`挂载组件,若是运行时编译(存在template,不存在render function)，则会进行编译，生成render函数。

### 1.2  compile()编译

compile阶段主要分为parse、optimize、和genarate阶段，最后生成render function

- **parse**: parse的作用是通过正则匹配整个template,来生成一个叫**抽象语法树**(AST，简单来说就是一个json对象)的东西。

- **optimize**:主要是用来**标记静态节点**。所谓静态节点简单理解就是页面中写死的部分(不涉及vue的内容)。标记静态节点的作用是再更新视图时，会有一个patch过程来对比新旧虚拟DOM,若是静态节点则可以直接跳过，这是vue中关于性能的一处优化。

- **genarate**:主要是**将AST,转换为render function字符串**。

### 1.3 响应式

vue在执行render function的时候会指向getter对数据进行依赖收集，所谓依赖收集就是为每一个data分配一个Dep,然后将依赖于这些数据的Watcher(一般一个vue组件对应一个Watcher)，添加到对应的Dep中。

### 1.4 虚拟DOM

当我们执行编译后的render function时，会生成一个叫虚拟DOM的东西。虚拟DOM简单来说其实就是一棵**以javascript对象(vNode节点)为基础**的树。这棵树其实是真实DOM的映射。最终可以通过一系列的操作生成真实DOM。

### 1.5 diff算法视图更新

vue主要通过patch方法来做这方面的优化，也就是我们通常说的diff算法。patch方法会接受新旧两个虚拟DOM作为参数。进而两者进行比对，找出不同的地方，然后再和真实dom做替换，这样极大的提高了性能。

### 1.6 nextTick

Vue.js在默认情况下，每次触发某个数据的setter方法后，对应的 Watcher 对象会被 push 进一个队列中，它会在下一次tick的时候将这些watcher拿出来，执行一遍对应的patch操作。



# 2 vue2.X和vue3.X渲染器的diff算法

diff算法有以下过程：

- 同级比较，再比较子节点
- 先判断一方有子节点一方没有子节点的情况（如果新的children没有子节点，将旧的子节点移出）
- 比较都有子节点的情况（核心diff）
- 递归比较子节点

vue将diff的时间复杂度从`O(n^3)`优化为`O(n)`，只有当新旧Children都为多个子节点时才需要用核心的Diff算法进行同层级比较。

**2.1 虚拟DOM**

由于在浏览器中操作DOM是很昂贵的。频繁的操作DOM，会产生一定的性能问题。这就是虚拟Dom的`产生原因`。

**Virtual DOM本质就是用一个原生的JS对象去描述一个DOM节点。是对真实DOM的一层抽象**。(也就是源码中的VNode类，它定义在src/core/vdom/vnode.js中。)

**2.2 vue2**

核心diff算法采用了`双端比较`的算法，同时从新旧children的两端开始进行比较，借助key值找到可复用的节点，再进行相关操作。

**2.3 vue3**

在创建VNode时就确定其类型，以及在`mount/patch`的过程中采用`位运算`来判断一个VNode的类型，在这个基础之上再配合核心的Diff算法，使得性能上较Vue2.x有了提升。

该算法中还运用了`动态规划`的思想求解最长递归子序列。

**2.4 key**

**「key的作用是尽可能的复用 DOM 元素。」**

新旧 children 中的节点只有顺序是不同的时候，最佳的操作应该是通过移动元素的位置来达到更新的目的。

需要在新旧 children 的节点中保存映射关系，以便能够在旧 children 的节点中找到可复用的节点。key也就是children中节点的唯一标识。



# 3 Vue中组件生命周期调用顺序

组件的调用顺序都是`先父后子`,渲染完成的顺序是`先子后父`。

组件的销毁操作是`先父后子`，销毁完成的顺序是`先子后父`。

### 3.1 加载渲染过程

父beforeCreate -> 父created -> 父beforeMount -> 子beforeCreate -> 子created -> 子beforeMount - > 子mounted -> 父mounted

### 3.2 子组件更新过程

父beforeUpdate -> 子beforeUpdate -> 子updated -> 父updated

### 3.3 父组件更新过程

父 beforeUpdate -> 父 updated

### 3.4 销毁过程

父beforeDestroy -> 子beforeDestroy -> 子destroyed -> 父destroyed