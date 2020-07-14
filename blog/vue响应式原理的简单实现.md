---
title: vue响应式原理的简单实现
date: 2020-07-10 11:41:07
tags: [javaScript]
category: [前端]
---

> 这次暑假实训第一尝试了react框架去实现一个前端项目，和之前惯用的vue相比，react的无状态组件和hook确实很香，组件化很彻底，代码整洁清晰，不过是单向数据流，使用起来略显麻烦，不像vue这种v-model指令简单粗暴，其实双向绑定也不难，我们就来自己实现一个vue响应式吧

# 1 vue和react实现响应式对比

react需要手动绑定onChange函数

``` javascript
const Test = (props) => {
    const {num,setNum} = useState(0);

    const changeNum = (e) => {
        setNum(e.target.value);
    }

    return (
        <div>
            <input type="text" value={num} onChange={(e) => {changeNum(e);}}>
        </div>
    )
}
```

vue只需绑定v-model指令即可

``` javascript
<template>
    <input type="text" v-model="num">
</template>
export default {
    name: 'Test',
    data() {
        return {
            num: 0
        }
    }
}
```

我们可以看到vue不需要`手写change`对应的函数即可完成`双向绑定`，下面我们就正式实现一个MVVM响应式框架

# 2 MVVM框架内部结构概述

### 2.1 分析vue双向绑定流程

写之前我们需要弄清楚vue实现数据双向绑定的原因：
- 首先，我们为所有data中的数据做`代理`，因为我们用this.num比this.data.num更简洁
- 然后为所有数据加上`拦截器`，当监测到数据变化时，我们需要动态更新这一数据所渲染的所有DOM中
- 想要实现更新，就要把数据和DOM之间建立关联，我们需要一个`Watcher`监视这种变化
- 我们知道，浏览器是不认识v开头的属性，所以非常关键的一点就是在运行vue框架的时候，会先做一步`模板编译`，将所有vue指令转化成浏览器认识的语法

### 2.2 MVVM类的结构

``` javascript
class MVVM {
    constructor() {}
    // 数据代理
    DataProxy() {}
    // 数据拦截
    _Observer() {}
    // 模板编译
    _compile() {}
    // 字符替换
    compileText() {}
}

class Watcher {
    constructor() {}
    // 更新视图
    update() {}
}
```

下面我们就来一步一步完成上述函数

# 代理与拦截

当我们new一个Vue实例的时候，会传入一些参数
``` javascript
const vm = new Vue({
    el: '#root',
    data: {
        text: 'I am panda',
        num: 20,
        user: {
            name: 'panda',
            age: 20
        }
    },
    methods: {
        showMe() {
            console.log(`I am ${user.name}, ${user.age} years old.`); 
        }
    }
})
```
Vue实例里面的这个对象参数，就是它初始化构造函数的`配置项`，我们先来实现构造函数

``` javascript
class MVVM {
    constructor(options = {}) {
        this.$el = document.querySelector(options.el);
        this.$data = options.data;
        this.methods = options.methods;
        // 监听的依赖列表
        this.depList = {}
        // 用this.xxx 代理 this.$data.xxx
        Object.keys(this.$data).forEach((key) => {
            this.proxyData(key);
        })
        this._Observer(this.$data);
        this._compile(this.$el);
    }
}
```
构造函数就是这样，按顺序执行几个步骤，监听的watcherTask作用会在后面提到。我们先实现第一步，数据代理

``` javascript
proxyData(key) {
    // 因为Object.defineProperty参数中的对象会影响作用域，导致this
    // 指向达到不我们的期望，所以提前定义that指向我们的MVVM实例
    const that = this;
    // 利用Object.defineProperty的get和set方法实现数据劫持
    Object.defineProperty(that, key, {
        configurable: false,
        enumerable: true,
        get() {
            // 当访MVVM实例下的该属性时，返回$data下的该属性对应的值
            // 实现了对数据的代理
            return that.$data[key];
        },
        set(newVal) {
            // 当a = 3这样的赋值语句执行时会触发set
            // 设置当该属性的值发生变化时更新对应$data下的属性值
            that.$data[key] = newVal;
        }
    })
}
```

我们使用了Object.defineProperty强大的拦截能力，完成了对数据的代理。我们同样可以使用Object.defineProperty的get和set做其他事情，想要更好的理解，我们可以模拟一个场景：

``` html
<div id="root">
    <div>{{num}}</div>
    <p>{{text}}</p>
    <input v-model="num" />
    <input v-model="num">
</div>
```
在这个html中，三个DOM元素相当于都绑定了num这一变量，当改变其中一个input的值后，其他两个元素中的值也会发生变化，这就是我们常说`双向绑定`

到底是如何实现呢？

形象一点来讲，我们可以把这一过程比喻为：
- 老师手里有一套试卷，他把试卷发给班级里的每位学生
- 一位学生发现试卷有一处错误并改正
- 他把错误和改正后的结果告诉老师
- 老师通知班级里其他的学生改正
- 这时大家的卷子是不是又一样了呢
- 当然，老师自己看出问题通知大家也是可以的

还原到我们的MVVM框架中就是：
- 试卷就是$data中的属性，学生就是绑定了这个属性的DOM元素，老师的就是我们接下来要说的`监听列表：depList`
- 上面的例子中，一个num对应了三个元素
- 当一个input修改了它的值后，通知给监听列表
- 监听列表会把num新的值传给其他两个元素
- 同样的，当我们通过js直接修改num，也会把新值传给3个元素

这个depList是如何做到**通知**这件事呢？因为它保存了data中属性和DOM元素的映射关系：
``` javascript
// 对于上面的例子
this.depList = {
    num: [Watcher(div),Watcher(input),Watcher(input)],
    text: [Watcher(p)]
}
```
**这样的话，input可以找到它绑定的num，进而找到其他绑定num的元素。在模板编译的时候，我们遍历整个结构，将所有依赖（用到data中属性的DOM节点称之为`依赖`）生成对应的`Watcher`（监听器）后，分类并放置在`depList`，在_Observer中再次使用set，当data里属性改变时，将消息传递给depList并更新它的值**

那么_Observer的内容就非常清晰辣。（**值得一提的是在vue3.0中，尤大选择了proxy语法来替换Object.defineProperty，因为proxy是元编程，它对不管是对象增加属性还是数组push、pop都可以正确的识别**）
``` javascript
_Observer(data) {
    const that = this;
    // 循环为data的每一个属性做响应式
    Object.keys(data).forEach(key => {
        let value = data[key];
        // 初始化该属性的监听列表
        this.depList[key] = [];
        Object.defineProperty(data, key, {
            configurable: false,
            enumerable: true,
            get() {
                return value;
            },
            set(newVal) {
                // 发现数据改变时
                if(newVal !== value) {
                    // 更新data中的数据
                    value = newVal;
                    // 通知该属性的所有依赖一同更新
                    that.depList[key].forEach(watcher => {
                        watcher.update();
                    })
                }
            }
        })
    })
}
```

既然我们提到了Watcher，不妨把它的代码实现一下。Wachter的功能是**建立num到某一个具体DOM元素的关系，他们是一对一的。**而depList的每个属性里是**一个数组，数组的每一项都是一个Watcher，建立起来了一对多的关系。**（如前面介绍的depList）

``` javascript
class Watcher {
    constructor(el, vm, key, type) {
        // el是绑定的元素
        this.el = el;
        // vm是MVVM的实例，通过参数传来this，拿到它的$data属性
        this.vm = vm;
        // 该元素绑定的属性
        this.key = key;
        // 赋值类型，比如div是innerHTML，input是value
        this.type = type;
        // 立即执行实现对数据的初始化
        this.update();        
    }
    update() {
        // 判断是否对象，即xxx.xxx形式
        const isObj = this.key.split('.').length > 1;
        // 如果是对象则循环赋值
        // 如果是简单属性直接赋值
        if (isObj) {
            let v = null;
            this.key.split('.').forEach((item, index) => {
                v = !v ? this.vm.$data[item] : v[item];
            })
            this.el[this.type] = v;
        } else {
            this.el[this.type] = this.vm.$data[this.key];
        }
        // 等于
        // input.value = this.$data.num
        // div.innerHTML = this.$data.num
    }
} 
```

我们如何是知道哪个DOM元素需要Watcher的？当然是我们使用v-model绑定的辣！不管是v-cloak、v-model还是v-for，都是浏览器不认识的，是`我们对html模板做的标记`，当编译器脚本进行模板编译时，遇到这些指令把它们`翻译`成我们需要的和我们认识的代码

（**知识补充：**DOM中有很多节点，普通元素节点，文本节点、注释节点等，比如<div>{{num}}</div>就是一个普通元素节点嵌套一个文本节点。对于不同的节点，在模板编译中有不同的策略）


``` javascript
_compile(el) {
    // 编写一个可递归函数，对根节点下的所有节点进行模板编译
    const nodes = el.childNodes;
    [...nodes].forEach(node => {
        // 如果是text节点
        if (node.nodeType === 3) {
            // 判断该节点是否为纯空格
            const text = node.textContent.trim();
            // 如果非空 渲染节点
            // 具体渲染策略和过程稍后再讲
            if (text) {
                this.compileText(node, 'textContent');
            }
        } else if (node.nodeType === 1) {
            //如果是普通节点则递归解析子节点
            if (node.childNodes.length > 0) {
                this._compile(node);
            }
            // 实现v-model
            if (node.hasAttribute('v-model') && (node.tagName === 'INPUT' || node.tagName === 'TEXTAREA')) {
                // 添加事件监听
                // 使用立即执行函数初始化Watcher
                node.addEventListener('input', (() => {
                    const key = node.getAttribute('v-model');
                    // 添加到监听列表
                    this.depList[key].push(new Watcher(node, this, key, 'value'));
                    node.removeAttribute('v-model');
                    return () => {
                        this.$data[key] = node.value;
                    }
                })())
            }
            // 实现v-html
            if (node.hasAttribute('v-html')) {
                const key = node.getAttribute('v-html');
                this.watcherTask[key].push(new Watcher(node, this, key, 'innerHTML'));
                node.removeAttribute('v-html');
            }
            // 实现@click事件
            if (node.hasAttribute('@click')) {
                const key = node.getAttribute('@click');
                node.removeAttribute('@click');
                node.addEventListener('click', e => {
                    // 如果存在该方法
                    this.methods[key] && this.methods[key].bind(this)()
                })
            }
            // 实现v-cloak
            if (node.hasAttribute('v-cloak')) {
                node.removeAttribute('v-cloak');
            }
        }
    })
}
```
在真正的vue框架中，这里有`模板编译->AST抽象语法树->虚拟DOM->diff算法->真实DOM`这几个阶段。我们complile的这个过程其实是化简过的，只能让它绑定Watcher和识别指令。我们还差最后一个功能，将{{num}}替换成num真正的值，刚才跳过的compileText就是实现这个功能的

``` javascript
compileText(node, type) {
    // 正则规则匹配{{}}
    const reg = /\{\{(.*?)\}\}/g;
    let txt = node.textContent;
    if (reg.test((txt))) {
        node.textContent = txt.replace(reg, (matched, key) => {
            // 判断是否对象，即xxx.xxx形式
            const isObj = key.split('.').length > 1;
            // 添加监听
            const dep = isObj ? this.depList[key.split('.')[0]] : this.depList[key];
            dep.push(new Watcher(node, this, key, type));
            // 如果是对象内部属性
            if (isObj) {
                let v = null;
                key.split('.').forEach((item, index) => {
                    v = !v ? this[item] : v[item];
                })
                return v;
            } else {
                return this[key];
            }
        })
    }
}
```

# 总结

1. 我们实现的这个双向绑定MVVM框架功能还很简陋，不能实现对象动态添加属性和数组添减项的变化侦测,等vue3.0出来我们可以学习一波proxy如何做这件事

2. 学会自造轮子可以提升封装的能力，对工具的封装、对函数的封装、对组件的封装都非常重要

3. 要学习源码，深入理解作者的思路和功能的底层原理，这样以后遇到业务问题也能从多维角度出发来分析了
