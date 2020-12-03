<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-12-01 11:28:19
 * @FilePath: \undefinedc:\Users\23163\Desktop\web\Blog\JavaScript\Promise.md
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

# 实现Promise.race

Promise.race(iterable) 方法返回一个 promise，一旦迭代器中的某个promise解决或拒绝，返回的 promise就会解决或拒绝

``` js
const p1 = new Promise((resolve, reject) => {
    setTimeout(() => {
         resolve('第一个任务');
    }, 200);
})
const p2 = new Promise((resolve,reject) =>{
    setTimeout(() => {
        reject('第二个任务')
    }, 1000);
})
const p3 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('第三个任务');    
    }, 500);
  })
Promise._race([p1,p2,p3]).then(
    result=>console.log(result)
  ).catch(
    e=>console.log(e)        
  )//第一个任务
  

Promise._race = function(promises) {
  return new Promise((resolve, reject) => {
    for(let i=0; i<promises.length;i++) {
      Promise.resolve(promises[i]).then((res) => {
        resolve(res);
      }, (err) => {
        return reject(err);
      })
    }
  })
}
```

# 实现Promise.race

``` js
Promise.finally = function(callback) {
  return this.then(res => return Promise.resolve(callback()).then(() => res),
  reason => return Promise.resolve(callback()).then(() => throw reason))
}
```

# 实现Promise
``` js

```
