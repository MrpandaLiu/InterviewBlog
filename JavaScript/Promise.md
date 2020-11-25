<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-11-24 17:30:20
 * @FilePath: \Blog\JavaScript\Promise.md
 * @Description: add some description
-->
# 实现Promise.all

**什么是Promise.all**

Promise.all(iterable) 方法返回一个 Promise 实例，此实例在 iterable 参数内`所有的 promise 都“完成（resolved）”`或`参数中不包含 promise `时回调完成（resolve）；如果`参数中  promise 有一个失败（rejected）`，此实例回调失败（reject），失败的原因是第一个失败 promise 的结果。

**实现**

``` js
let promise1 = new Promise(function(resolve) {
  resolve(1);
});
let promise2 = new Promise(function(resolve) {
  setTimeout(() => {
    resolve(2);
  },1000)
});
let promise3 = new Promise(function(resolve) {
  resolve(3);
});

Promise._all = function(promises) {
  const result = [];
  let promiseCount = 0;
  return new Promise((resolve, reject) => {
    for(let i=0; i<promises.length; i++) {
      Promise.resolve(promises[i]).then((res) => {
        promiseCount++;
        result[i] = res;
        if(promiseCount === promises.length) {
          resolve(result);
        }
      })
    }
  }, (err) => {
    reject(err);
  })
}

let promiseAll = Promise._all([promise1, promise2, promise3]);
promiseAll.then(function(res) {
  console.log(res);
});
```