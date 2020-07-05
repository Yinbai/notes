# Promise、Async、Await

> Async 和 Await 是 Promise 的语法糖



## Promise



## Async / Await

在事件循环中的执行流程：

1. 一个函数被定义为 async，表示它将被作为一个 Promise 对象返回，可以调用 `.then()`，函数 return 返回值作为 .then() 的参数
   - 如果这个 async 函数内 return 的是一个 Promise 对象，那么这个函数的返回值就是这个 Promise 对象
     - 如果 return 的 Promise 对象不是 fulfilled 状态，那么这个函数的返回值将不会调用 .then()
   - 如果这个 async 函数内 return 的是一个非 Promise 值，那么会将这个值按照 Promise.resolve() 包装成 Promise 对象
2. async 函数内部的 await 关键字根据它右边的表达式是否是异步函数有不同的表现：
   - 如果 await 右侧的表达式是一个非异步函数的调用，就相当于直接把 await 下面的代码注册为一个微任务，可以简单理解为 promise.then(await 下面的代码)，然后跳出 async 函数，执行其他代码
   - 如果 await 右侧的表达式是一个异步函数的调用，那么执行完 await 右侧的代码之后，会直接跳出 async 函数，去执行其他代码，在本轮宏任务和微任务执行完成之后，再去执行 await 下面的代码

看一段代码，加深理解：

```javascript
// await 右侧是一个非异步函数调用
console.log('script start')		// 1

async function async1() {
  await async2() // async2 是一个非异步函数
  console.log('async1 end')		// 5 ... 第 1 个被加入微任务队列
}
async function async2() {
  console.log('async2 end')		// 2
}
async1()

setTimeout(function () {
  console.log('setTimeout')		// 8 ... 第 1 个被加入宏任务队列
}, 0)

new Promise((resolve) => {
  console.log('Promise')			// 3
  resolve()
})
  .then(function () {
  console.log('promise1')			// 6 ... 第 2 个被加入微任务队列
})
  .then(function () {
  console.log('promise2')			// 7 ... 第 3 个被加入微任务队列
})

console.log('script end')			// 4
```

```javascript
// await 右侧是一个异步函数调用
console.log('script start') 		// 1

async function async1() {
  await async2() // async2 是一个异步函数
  console.log('async1 end') 		// 8 ... 执行完本轮同步任务和微任务之后才会执行
}
async function async2() {
  console.log('async2 end') 		// 2
  return Promise.resolve().then(() => {
    console.log('async2 end1') 	// 5 ... 第 1 个被加入微任务队列
  })
}
async1()

setTimeout(function () {
  console.log('setTimeout') 		// 9 ... 第 1 个被加入宏任务队列
}, 0)

new Promise((resolve) => {
  console.log('Promise') 				// 3
  resolve()
})
  .then(function () {
  console.log('promise1') 			// 6 ... 第 2 个被加入微任务队列
})
  .then(function () {
  console.log('promise2') 			// 7 ... 第 3 个被加入微任务队列
})

console.log('script end') 			// 4
```

