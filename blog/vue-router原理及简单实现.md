<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-08-30 20:06:13
 * @FilePath: \yunniubaoc:\Users\23163\Desktop\web\Blog\blog\vue-router原理及简单实现.md
 * @Description: add some description
-->
# 1 vue-router原理

## 1.1 概念

在vue和react中，我们都可以通过控制url的改变，在不刷新（重新请求）页面的情况下，更新页面视图。

## 1.2 实现方式

我们知道vue-router的配置项中可以设置mode为`history`或`hash`

- **Hash** --- 利用URL中的hash('#');

- 利用**History interface**在HTML5中新增的方法

下面我们以`mrpanda.com`网站下的`/register`和`/login`路由为例，来分别剖析下这两种模式是如何进行路由变化的

**Hash模式**

在hash模式下，前端每次请求的是#前面的内容，比如`mrpanda.com/#/register`和`mrpanda.com/#/login`请求的都是`mrpanda.com`这个url的内容，我们知道，在vue项目的生产环境中，我们请求根目录都会返回index.html，在这个模板html中挂载了处理router的脚本，这个脚本监测到路由后进行一系列操作最终将页面渲染

**History模式**

在history模式下，我们发现没有了那个烦人的#符号，在本地测试也是效果同hash模式，但当你部署到服务器后，就会发现除了根页面都无法访问，这是为什么呢？因为history是请求的整个URL————`mrpanda.com/register`，很显然，我们的路由是相当于一个get请求，服务器上显然是没有这个register的，那么就会发生前面提到的出错。那为什么根页面没问题？因为根页面，我们反向代理了index.html啊！那么问题也就迎刃而解————在history模式下时，访问任何`mrpanda.com`同源的内容时，都返回index.html，后面router脚本的执行就和hash模式大同小异了

不过是hash和history都只是个代号，真正对路由控制的是HashHistory对象和HTML5History对象

## 1.3 HashHistory对象

hash（"#"）符号的本来作用是加在url中指使网页中的位置（锚点），#符号本身以及它后面的字符称之为hash。它具有以下特点：

1. hash出现在url中，但不会被包含在请求中

2. hash的改变可以被监听到，可以通过window.location提供的api来操作它

3. 每次改变会记录到浏览器的访问历史栈中


下面我们来看Hash模式下请求一个页面的完整流程：

``` js
// vue-router： 调用router实例的方法，请求页面
$router.push();
// history底层对象：将上一步请求的url设置hash，然后添加到浏览器记录中
HashHistory.push();
// vue-router：监测url是否发生改变
History.transitionTo();
// vue-router：如果发生变化则更新路由
History.updateRoute();
// vue：替换当前app的路由
{ app._route = route }
// vue：根据路由渲染页面，更新视图
vm.render()
```

## 1.4 HTML5History对象

History interface是浏览器历史记录栈提供的接口，通过back()，forward()，go()等方法，以读取浏览器历史记录栈的信息，进行各种跳转操作。

从HTML5开始提供了两个新方法：

``` javascript
window.history.pushState(stateObject, title, url)
window.history.replaceState(stateObject, title, url)

// stateObject: 当浏览器跳转到新的状态时，将处罚popState事件，该事件将携带这个stateObject参数的副本
// title: 所添加记录的标题
// url: 所添加记录的url
```

## 1.5 HTML5History的优势

1. 颜值更高

2. url修改更广泛，hash模式只能修改#后面的

3. stateObject可以传各种类型的参数，同时还有一个title属性

4. history自动集成了未找到返回404的功能


# 2 Hash模式的模拟实现

下面我就来简单实现一下hash模式下的vue-router，代码非常简单，但是重要的是这个流程是怎么样的。

``` js
  // 在正式的vue-router中 是用h函数进行虚拟dom转换
  // 这里简化下操作直接无脑渲染
  const renderElement = (component) => {
    document.querySelector('router-view').innerHTML = component;
  }

  class VueRouter {
    constructor(opts) {
      this.options = opts || [];
      this.current = "/";
      this.routeMap = {};
    }

    // 初始化路由
    init() {
      this.transitionTo();
      this.createMap();
      this.render(renderElement);
      console.log(this.routeMap);
    }

    // 封装push
    push(path) {
      window.location.hash = path;
    }

    // 封装replace
    replace(path) {
      window.location.replace(`/#` + path);
    }

    // 监听hash改变
    transitionTo() {
      window.addEventListener('hashchange', this.updateRoute.bind(this));
    }

    // 建立路由和组件的映射关系
    createMap() {
      this.options.forEach(item => {
        this.routeMap[item.path] = item;
      })
    }

    // 更新路由
    updateRoute() {
      this.current = window.location.hash.slice(1);
      this.render(renderElement)
    }

    // 渲染视图
    render(h) {
      return h(this.routeMap[this.current].component);
    }
  }

  // 路由关系
  const routes = [{
    path: '/',
    component: `<div>
          this is root
        </div>
        <ul>
          <li><a href="/#/panda">panda</a></li>
          <li><a href="/#/cat">cat</a></li>
        </ul>`
  }, {
    path: '/panda',
    component: `<div>this is panda</div>`
  }, {
    path: '/cat',
    component: `<div>this is cat</div>`
  }];

  // 初始化路由
  const router = new VueRouter(routes);
  router.init();

  const goCat = () => {
    router.push(`/cat`);
  }
  btn.addEventListener('click', goCat);
```