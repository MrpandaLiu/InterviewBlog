<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-07-25 15:44:06
 * @FilePath: \yangbao-2019-developc:\Users\23163\Desktop\web\Blog\blog\vue和react区别.md
 * @Description: add some description
--> 
> 作为使用过vue和react开发的前端在面试难免会问到他们的区别，特此总结

# 1 核心思想不同

vue：渐进式开发、数据可变、双向数据绑定

react：函数式编程（纯组件），数据不可变、单向数据流

## 1.1 写法不同

vue：template模板、单文件vue

react：JSX、all in js

## 1.2 api差异

vue：指令、options API（watch、computed）

react：核心只有虚拟DOM + diff算法

## 1.3 社区差异

vue：vuex、vue-router、vue-cli由vue官方主导开发和维护

react：只关注底层，上层应用解决方案基本不插手

# 2 组件实现不同

vue：把options挂载到vue核心类上，然后再new Vue({options})拿到实例。this指向Vue实例，对用户不透明

react：定义render函数生成VNode，内部使用了四大组件类包装VNode，this指向用户自己定义的类，对用户透明

# 3 响应式原理不同

vue：依赖收集、自动优化

react：基于状态机、手动优化

# 4 diff算法不同

流程思维类似：

1. 不同的组件产生不同的DOM结构。当type不相同时，对应DOM操作就是直接销毁老DOM，创建新的DOM

2. 同一层次的一组子节点，可以通过唯一的key区分

vue：使用双向链表，边对比，边更新DOM

react：使用diff队列保存需要更新哪些DOM，得到patch树，再统一操作批量更新DOM

# 5 事件机制不同

vue：原生事件使用标准web事件

react：原生事件被包装。所有事件都冒泡到顶层document监听