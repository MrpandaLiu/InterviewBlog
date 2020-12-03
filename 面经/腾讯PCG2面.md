<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-12-03 16:37:29
 * @FilePath: \undefinedc:\Users\23163\Desktop\web\Blog\面经\腾讯PCG2面.md
 * @Description: add some description
-->
1. span的宽高由什么决定？想设置宽高需要先设置什么属性？文字居中使用属性？经过上述设置后，一段文本超过了第一行，那么第二行是处于什么位置？如何让超出的这一行居左？

2. 谈一下盒模型和box-sizing？谈一下element-ui中的盒模型

3. 使用content-box，如何获取content+padding+border的宽度

4. 从输入url到页面渲染发生了什么？

5. 本地的DNS存在哪里？浏览器中是否有DNS缓存？

6. 谈一下TCP（主要和长连接有关）。浏览器可以同时发起几个请求？

7. 什么会阻塞DOM的渲染？CSS中请求url图片时是否会阻塞渲染？

8. 如何让JS不阻塞渲染？两种方法的区别是什么？

9. 什么是回流和重绘？什么情况下会产生回流和重绘？在此期间浏览器会做什么事情

10. 为什么vue无法监测数组的改变

11. 使用typescript有什么好处？是否经常使用any？

12. 什么是asnyc await？读代码说结果（后面又加上了setTimeout、await继续说结果）
``` js
let a = 1;
asnyc function test() {
  a = 2;
}
test();
console.log(a);
``` 

13. 手写一个CSS三角形

14. 手写实现一个链式调用的类 `hero.kill(1).blood(10)`