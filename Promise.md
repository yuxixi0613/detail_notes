[TOC]

# Promise的then方法



- `Promise.prototype.then` 可以接受两个回调函数作为参数， 捕获promise成功或失败的状态，执行成功或者失败的回调。

- 成功和失败的回调函数接受一个参数，是promise实例的值

- then方法的回调函数是异步的，当调用then的实例化的promise实例不再是pending状态的时候，会执行then中的对应的成功或者失败的回调函数

- 因为then是解决异步嵌套的核心流程，所以then方法一定返回的是promise对象

- then的返回值：

  - 成功的promise实例调用then方法
    - 默认：then返回的是一个成功的promise实例,promise实例的值就是当前回调函数的返回值
    - 当then中返回的是一个promise实例的时候，then返回的promise实例的状态和值就是回调函数返回的promise实例的状态和值
    - 当then的回调函数内部出现报错并且没有异常处理，则直接then返回失败的promise实例，值为这个错误
    - then中只传递一个值，会直接发生值穿透，then返回成功状态的promise实例，值是调用then的promise实例的值
  - 失败的promise对象调用then方法
    - 当then中没有书写关于失败的回调函数的时候，会发生值穿透，直接返回失败的，值为调用catch的失败的promise实例的值
    - 当then中书写了处理失败的回调函数，则返回值规则和成功状态then一样

  

   - 成功的promise对象调用catch方法

   - catch直接返回一个成功的promise对象，并且值是调用catch的那个成功的promise对象的值

  ```js
  const promise = new Promise((resolve, reject) => {
      setTimeout(() => {
          console.log("setTimeout()");
          // console.log(333);
          resolve('a数据');
          // reject("失败了~");
      }, 1000);
  });
  promise.then(
      (result) => { // 成功的回调
          // 当promise对象的状态变成resolved，就会执行当前函数
          // console.log("resolved 111");
          console.log(result);
          return "then";
      },(error) => { // 失败的回调
          // 当promise对象的状态变成rejected，就会执行当前函数
          // console.log("rejected 111");
          console.log(error);
      }
  )Copy to clipboardErrorCopied
  ```





# Promise的catch方法

- `Promise.prototype.catch`可以接受一个回调函数作为参数， 捕获promise失败的状态，执行的回调。
- 规则和then方法中的 第二个回调函数一致
- 成功的promise对象调用catch方法，catch直接返回一个成功的promise实例，并且值是调用catch的那个成功的promise实例的值







# Promise的finally方法

- 无论promise实例成功和失败都会进入finally中执行，当你书写的promise对象无论成功还是失败，都想要执行一段代码的时候，finally就是最佳解决方案
- finally的回调函数不接受参数
- finally的返回值
  - 当finally中的回调函数返回的是失败的promise对象的时候，则finally返回失败状态的promis对象，值为回调函数返回promise对象的值
  - 当finally的回调函数中出现报错，则finally直接返回失败的promise对象，值为错误信息
  - 其他情况一律认为类似穿透

```js
const promise = new Promise((resolve, reject) => {
    // resolve(111);
    // reject(222);
});
// promise变成成功/失败都触发，pending不触发
promise.finally(() => {
    console.log("finally()");
});
```

















------







# Promise静态方法



### Promise的all方法：

>   \- 如果all中所有的promise都成功，则all返回的promise对象是成功的状态，值所有监听的promise的值组成的数组
>
>   \- 如果all中有一个promise失败，则all返回的promise对象是失败状态，值为监听的失败的promise那个错误信息



### Promise的allSettled方法：

>   \- 主要是为了检测所有的promise对象是否执行结束（无论成功还是失败）
>
>   \- allSettled方法永远返回成功的promise状态，值是一个数组，数组的每一个值是监听的promise对象的状态和值组成的新对象



### Promise的race方法：

>   \- 返回的promise对象的状态和值 由所有监听的promise对象中第一个改变状态（无论成功还是失败）的promise决定



### Promise的any方法：

>   \- 返回一个promise对象状态和值是 执行最快的并且成功promise的状态和值
>
>   \- 如果所有的promise全部失败，则返回一个新的错误类型AggregateError：All promises were rejected



### Promise.resolve

>   \- 快速的得到一个成功的promise对象，值为resolve的参数
>
>   \- 当resolve的参数是一个promise对象的时候，resolve的返回值就和这个promise对象挂钩了



### Promise.reject

>   \- 快速的得到一个失败的promise对象，值为reject的参数
>
>   \- 当reject的参数是一个promise对象的时候，并没有挂钩，和上边逻辑一致，这个promise对象参数直接作为reject返回的promise对象值







------









# async和await



### async和await：  

>   \- 专门用来解决异步编程问题，是终极解决方案，用同步表达异步操作
>
>   \- async是用来声明某个函数是异步函数（认为是generator中的*）,await是等待一个异步的执行（相当于是yield）



### async函数的返回值

>   \- async函数返回promise对象
>
>   \- 默认返回成功的promise对象,值为函数的返回值
>
>   \- 当async函数内部出现错误的时候，则async函数返回失败的promise对象，值为错误信息
>
>   \- 当async函数内的await等到了一个失败的promise对象时，则async函数直接返回一个失败的promise对象，值为错误信息
>
>   \- 概括（失败：1. async中报错了还没有处理  2. async中等待了一个失败的promise对象）

 

### await返回值：

>   \- await等待的成功promise对象的值







