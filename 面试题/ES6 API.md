# ES6 API

## 数组





## Promise

> 1. 只有三种状态，一旦确定就不可更改
> 2. 每次 .then() 或 .catch() 会返回新的 Promise 对象，如果值不是 Promise 对象，也会被包装成 Promise.resolive('xxx')
> 3. 每次 .then() 或 .catch() 不能返回当前的 Promise 对象，会报错
> 4. 



#### 创建 Promise 对象

```javascript
new Promise((resolve, reject) => {
	// 构造函数的参数是一个函数，会立即执行
})
```



#### 属性

#### 原型函数

Promise.prototype.then()

Promise.prototype.catch()

Promise.prototype.finally()

#### 静态方法

Promise.all()

> 接收一组 Promise 实例，包装成一个新的 Promise 实例，返回一个数组，数组成员对应参数返回的结果。参数可以不是数组，但必须有 Iterator 接口；如果数组元素不是 Promise 实例，会调用 Promise.resolve() 将参数转换成 Promise 实例。

此函数返回的结果有两种：

1. 作为参数的 Promise 实例的状态全部变为 resolved，此时 Promise.all() 的状态就是 resolved
2. 作为参数的 Promise 实例中，如果有一个变成 rejected，此时 Promise.all() 的状态就是 rejected
3. 作为参数的 Promise 实例中，如果变为 rejected 状态的实例没有调用 .catch() 方法，则会调用 Promise.all() 的 .catch()，并且只会调用一次

应用场景：

1. 同时请求多个互不依赖的数据源，可以使用一个 Loading 标记来加载多个模块



Promise.race()

> 和 Promise.all() 一样，接收一组 Promise 实例，包装成一个新的 Promise 实例，返回第一个执行完成的异步操作的结果，其他异步操作仍在进行，但是结果会被丢弃。

此函数返回的结果只有一种：

1. 第一个执行完成的异步操作的结果

应用场景：

1. 针对请求超时的情况进行处理，设定一个请求超时的时间，超时后进行其他处理



Promise.allSettled() **ES2020 引入**

> 接收一组 Promise 实例，包装成一个新的 Promise 实例。一旦所有参数实例的异步操作完成，状态会变为 fulfilled，不会变成 rejected。返回的 Promise 实例的监听函数接收到的是和每个参数对应的 Promise 实例组成的对象数组。

此函数需要等待所有的异步操作完成，一旦所有 Promise 操作完成，状态会变为 fulfilled，不会变成 rejected。

返回的 Promise 实例在 .then() 时的结果是：

```javascript
// [
//    { status: 'fulfilled', value: 42 },
//    { status: 'rejected', reason: -1 }
// ]
```

应用场景：

1. 适用于不关心异步操作的结果，只关心是否完成全部操作的场景，比如识别出批量请求中哪些失败或者哪些成功了。



Promise.any()

> 接收一组 Promise 实例，包装成一个新的 Promise 实例，只要参数实例中有一个的状态变成 resolved，包装实例就会变成 resolved 状态。
>
> 目前还是提案阶段



Promise.resolve()

Promise.reject()

Promise.try()

