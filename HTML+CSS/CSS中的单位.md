<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-11-11 21:40:09
 * @FilePath: \yunniubaoc:\Users\23163\Desktop\web\Blog\blog\HTML+CSS\CSS中的单位.md
 * @Description: add some description
-->
## 1 px

- px就是pixel像素的缩写，相对长度单位，网页设计的基本单位。像素是相对于显示器屏幕分辨率而言的。

## 2 em

- em是相对长度单位。相对于当前对象文本的字体尺寸（参考物是父元素的font-size）

- 如果当前父元素的字体尺寸未设置，则相对于浏览器的默认字体尺寸

- 特点：

  1. em的值并不是固定的；
  2. em会继承父级元素的字体大小

## 3 rem

- rem是相对于HTML根元素的字体大小（font-size）来计算的长度单位

- 如果没有设置HTML的字体大小，就会以浏览器默认字体大小，一般是16px

``` css
html {
  font-size: 62.5%;   /* 10 / 16 * 100% = 62.5% */
}

body {
  font-size: 1.4rem;  /* 1.4 * 10px = 14px */  
}
```

- 优点是，只需要设置根目录的大小就可以把整个页面的比例调好

**em和rem的区别**：
- rem是相对于根元素（HTML）的字体大小，而em是相对于其父元素的字体大小

**两者使用规则**：
- 如果这个属性根据它的font-size进行测量，则使用em
- 其他的一切事物属性均使用rem

## 4 vw、vh、vm

- vw、vh、vmax、vmin、vm这四个单位都是基于视口

- vw是相对视口（viewport）的宽度而定的，长度等于视口宽度的1/100

- vh是相对视口（viewport）的高度而定的，长度等于视口高度的1/100

- vmin和vmax是相对于视口的高度和宽度两者之间的最小值和最大值

- vm相对于视口的宽度或高度中较小的那个，其中最小的那个被均分为100单位的vm

## 5 %

- 对于普通定位元素就是我们理解的父元素

- 对于position：absolute;的元素是相对于已定位的父元素

- 对于position：fixed;的元素是相对于ViewPort
