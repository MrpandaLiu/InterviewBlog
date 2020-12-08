<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-12-08 22:05:44
 * @FilePath: \yunniubaoc:\Users\23163\Desktop\web\Blog\node.js+操作系统\浅谈webpack.md
 * @Description: add some description
-->
# 1 什么是构建工具（webpack可以做什么）？

> 我们感叹前端发展如此之快——CommonJS、ES6、CSS预处理器、Typescript、angular/react/vue等新技术层出不穷，各种提高开发效率的工具和框架如雨后春笋般涌现，但它们都有一个共同的问题，源码不能直接被浏览器识别，需要转换后才能运行。

所以我们需要一个构建工具，把上述的这些新东西转化为浏览器认识HTML、CSS、Javascript代码，包括如下内容：

- **代码转换**：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等。
- **文件优化**：压缩 JavaScript、CSS、HTML 代码，压缩合并图片等。
- **代码分割**：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载。
- **模块合并**：在采用模块化的项目里会有很多个模块和文件，需要构建功能把模块分类合并成一个文件。
- **自动刷新**：监听本地源代码的变化，自动重新构建、刷新浏览器。
- **代码校验**：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过。
- **自动发布**：更新完代码后，自动构建出线上发布代码并传输给发布系统。

构建是工程化、自动化在前端开发中的体现。

# 2 webpack中module，chunk和bundle的区别是什么？

- 在src目录下，我们手写的一个个文件，无论是EcmaScript还是CommonJS，它们都是**module**。
- 当我们写的module源文件传到webpack进行打包时，webpack会根据文件引用关系生成**chunk**文件，webpack会对这个chunk文件进行一些操作。
- webpack处理好chunk文件后，最后会输出**bundle**文件，这个bundle文件包含了经过加载和编译的最终源文件，所以可以直接在浏览器中运行。

# 3 loader和plugin

## 3.1 什么是loader？

`loader`是一个转换器，将A文件进行编译成B文件，单纯的文件转换过程。webpack自身只支持js和json两种格式的文件，对于其他文件需要通过loader将其转换为commonJS规范的文件后，webpack才可以解析。

## 3.2 loader的特征
- loader的执行顺序和代码书写的顺序是相反的，即：从下至上，从右至左。
- 第一个执行的loader会接收源文件作为参数，下次执行的loader会接收前一个loader执行的返回值作为参数。
- 需要严格遵循“单一职责”原则，即每个loader只负责自己需要负责的事情。

## 3.3 编写一个loader

``` html
<p>我的名字是name</p>
<p>我来自HZAU</p>
```

想将上述文件中的name全部修改成panda就可以使用loader

``` js
module.exports = function(source) {
  var content = "";
  content = source.replace("/name/g", "panda");
  return content;
}
```

## 3.4 什么是plugin?

`plugin`是一个扩展器，它丰富了webpack本身，针对是loader结束后，webpack打包的整个过程，它并不直接操作文件，而是基于事件机制工作，会监听webpack打包过程中的某些节点，执行广泛的任务。

## 3.5 plugin的特征

- 是一个独立的模块。
- 模块对外暴露一个js函数。
- 函数的原型（`prototype`）上定义了一个注入`compiler`对象的`apply`方法。  
- `apply`函数中需要有通过`compiler`对象挂载的webpack事件钩子，钩子的回调中能拿到当前编译的`compilation`对象，如果是异步编译插件的化可以拿到回调callback。
- 完成自定义子编译流程并处理`compilation`对象的内部数据。
- 如果异步编译插件的话，数据处理完成后执行callback回调。

**事件流机制**

webpack本质上是一种事件流的机制，它的工作流程就是将各个插件串联起来，而实现这一切的核心就是`Tapable`。

`Tapable`也是一个小型的library，是webpack的一个核心工具。类似于node中的events库，核心原理就是一个`订阅发布模式`。作用类似于插件接口。方法如下：

``` js
// 广播事件
compiler.apply('event-name', params);
compilation.apply('event-name', params);

// 监听事件
compiler.plugin('event-name', function(params) {});
compilation.plugin('event-name', function(params) {});
```
`Tapale`为webpack提供了统一的钩子类型定义，目前有10种hooks。

## 3.6 编写一个plugin

一个webpack插件由以下组成：
- 一个JavaScript命名函数。
- 在插件函数的prototype上定义一个apply方法。
- 指定一个绑定到webpack自身的事件钩子。
- 处理webpack内部实例的特定数据。
- 功能完成后调用webpack提供的回调。

``` js
class HelloPlugin {
  constructor(options) {
    this.options = options;
  }
  // webpack会调用HelloPlugin实例的apply方法给插件实例传入compiler对象
  apply(compiler) {
    compiler.hooks.done.tap('MyWebpackPlugin', () => {
      console.log(this.options);
    })
    // 如果事件是异步的，会带两个参数，第二个参数为回调函数
    compiler.plugin('done', (compilation, callback) => {
      // 处理完毕后执行callback以通知webpack
      // 如果不执行callback，运行流程将会一直卡在这不往下执行
      callback();
    })
  }
}
module.exports = HelloPlugin;
```

## 4 Webpack常用功能

## 4.1 DevServer

DevServer可以帮你方便的完成下面的事情：
1. 提供HTTP服务而不是使用本地文件预览；
2. 监听文件的变化并自动刷新网页，做到实时预览（不重新加载整个网页更新则使用`模块热替换`）；
3. 支持`Source Map`，方便调试；

**实时预览**

1. 通过 DevServer 启动的 Webpack 会开启监听模式，当发生变化时重新执行完构建后通知 DevServer。 
2. DevServer 会让 Webpack 在构建出的 JavaScript 代码里注入一个代理客户端用于控制网页，网页和 DevServer 之间通过 WebSocket 协议通信， 以方便 DevServer 主动向客户端发送命令。 
3. DevServer 在收到来自 Webpack 的文件变化通知时通过注入的客户端控制网页刷新。

## 4.2 HappyPack

HappyPack 就能让 Webpack 做到这点，它把任务分解给多个子进程去并发的执行，子进程处理完后再把结果发送给主进程。让 Webpack 同一时刻处理多个任务，发挥多核 CPU 电脑的威力，以提升构建速度。

[使用方式](http://www.xbhub.com/wiki/webpack/4%E4%BC%98%E5%8C%96/4-3%E4%BD%BF%E7%94%A8HappyPack.html)

## 4.3 ParallelUglifyPlugin

在使用 Webpack 构建出用于发布到线上的代码时，都会有压缩代码这一流程。 最常见的 JavaScript 代码压缩工具是 UglifyJS。
由于压缩 JavaScript 代码需要先把代码解析成用 Object 抽象表示的 AST 语法树，再去应用各种规则分析和处理 AST，导致这个过程计算量巨大，耗时非常多。
ParallelUglifyPlugin 则会开启多个子进程，把对多个文件的压缩工作分配给多个子进程去完成，每个子进程其实还是通过 UglifyJS 去压缩代码，但是变成了并行执行。 所以 ParallelUglifyPlugin 能更快的完成对多个文件的压缩工作。

[使用方式](http://www.xbhub.com/wiki/webpack/4%E4%BC%98%E5%8C%96/4-4%E4%BD%BF%E7%94%A8ParallelUglifyPlugin.html)

## 4.4 Tree Shaking

Tree Shaking 可以用来剔除 JavaScript 中用不上的死代码。它依赖**静态的 ES6 模块化语法**，例如通过 import 和 export 导入导出。

例如：utils.js中有A、B两个函数，main.js中依赖了utils中的A函数，那么打包后utils就仅含有A函数。

[使用方式](http://www.xbhub.com/wiki/webpack/4%E4%BC%98%E5%8C%96/4-10%E4%BD%BF%E7%94%A8TreeShaking.html)

## 4.5 Code Splitting（实现按需加载）

Code Splitting是webpack的一个重要特性，他允许你将代码打包生成多个bundle。对多页应用来说，它是必须的，因为必须要配置多个入口生成多个bundle；对于单页应用来说，如果只打包成一个bundle可能体积很大，导致无法利用浏览器并行下载的能力，且白屏时间长，也会导致下载很多可能用不到的代码，每次上线用户都得下载全部代码，Code Splitting能够将代码分割，实现按需加载或并行加载多个bundle，可利用并发下载能力，减少首次访问白屏时间，可以只上线必要的文件。

三种方式切割代码：
1. 多entry方式
2. 公共提取
3. 动态加载

**多entry方式**

这种方式就是**指定多个打包入口**，从入口开始将所有依赖打包进一个bundle，每个入口打包成一个bundle。此方式特别适合多页应用，我们可以每个页面指定一个入口，从而每个页面生成一个js。

``` js
entry: {
  page1: './src/page1.js',
  page2: './src/page2.js'
},
```

**公共提取**

将公共模块提取出来生成一个bundle，公共模块意味着有可能有很多地方使用，可能导致每个生成的bundle都包含公共模块打包生成的代码，造成浪费，将公共模块提取出来单独生成一个bundle可有效解决这个问题。

``` js
optimization: {
  splitChunks: {
    chunks: 'all'
  }
}
```

**动态加载**

在给单页应用做按需加载优化时，一般采用以下原则：

- 把整个网站划分成一个个小功能，再按照每个功能的相关程度把它们分成几类。
- 把每一类合并为一个 Chunk，按需加载对应的 Chunk。
- 对于用户首次打开你的网站时需要看到的画面所对应的功能，不要对它们做按需加载，而是放到执行入口所在的 Chunk 中，以降低用户能感知的网页加载时间。
- 对于个别依赖大量代码的功能点，例如依赖 Chart.js 去画图表、依赖 flv.js 去播放视频的功能点，可再对其进行按需加载。

被分割出去的代码的加载需要一定的时机去触发，也就是当用户操作到了或者即将操作到对应的功能时再去加载对应的代码。 被分割出去的代码的加载时机需要开发者自己去根据网页的需求去衡量和确定。

由于被分割出去进行按需加载的代码在加载的过程中也需要耗时，你可以预言用户接下来可能会进行的操作，并提前加载好对应的代码，从而让用户感知不到网络加载时间。

``` js
output: {}
  // 为动态加载的 Chunk 配置输出文件的名称
  chunkFilename: '[name].js',
}
```

## 4.6 babel-loader

babel是一个转译器，将ES6转换成ES5。babe的转译过程分为三个阶段：**parsing**、**transforming**、**generating**。以ES6转译ES5为例：
> ES6代码输入 ==》 解析得到AST ==》 plugin用babel-traverse对AST树进行遍历转译 ==》 得到新的AST树 ==》 用babel-generator通过AST树生成ES5代码

，babel只是转译新标准引入的语法，比如ES6的箭头函数转译成ES5的函数；**而新标准引入的新的原生对象，部分原生对象新增的原型方法，新增的API等（如Proxy、Set等），这些babel是不会转译的。需要用户自行引入polyfill来解决**

> 使用babel-polyfill会把ES2015+环境整体引入到你的代码环境中，让你的代码可以直接使用新标准所引入的新原生对象，新API等。