# Javascript 面试题

题目预览

- [1. Javascript 的数据类型有哪些 ?](#javascript-的数据类型有哪些)
- [2. 基本数据类型和引用数据类型有什么区别 ?](基本数据类型和引用数据类型有什么区别)
- [3. &&、||、 !!  运算符分别能做什么 ?](%26%26-%7C%7C-!!-运算符分别能做什么)
- [4. typeof 的作用是什么 ?](typeof-的作用是什么)
- [5. instanceof 的作用是什么 ?](instanceof-的作用是什么)
- [6. constructor 的作用是什么 ?](constructor-的作用是什么)
- [7. undefined 和 undeclared 的区别是什么 ?](undefined-和-undeclared-的区别是什么 )
- []()
- []()
- []()
- []()
- []()
- [4. new 关键字都做了什么 ?](new-关键字都做了什么)
- []()
- []()
- []()
- []()
- []()
- []()
- []()
- []()
- []()
- []()
- []()
- []()
- []()
- []()
- [说一说 Javascript 数据类型转换](说一说-javascript-数据类型转换)

- [一段 Javascript 代码是如何执行的 ?](#一段-javascript-代码是如何执行的)



### Javascript 的数据类型有哪些

7 种基本数据类型：Undefined、Null、Boolean、Number、String、Symbol（es6 新增，表示独一无二的值）和 BigInt（es10 新增）
1 种引用数据类型：Object，包含 Function、Array、Date 等

### 基本数据类型和引用数据类型有什么区别

基本数据类型：直接存储在栈（stack）中，占据空间小、大小固定，属于被频繁使用数据，所以放入栈中存储。

引用数据类型：同时存储在栈（stack）和堆（heap）中，占据空间大、大小不固定。引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。

### 说一说 Javascript 的类型转换

### Javascript 中数据类型的判断（typeof、instanceof、constructor、toString）

```javascript
// typeof，对于基本数据类型来说，除了 null，其他类型都能正确判断
typeof '1' === 'string'
typeof null === 'object'
typeof {} === 'object'
typeof function() {} === 'function'

```

```javascript
// instanceof，object instanceof Contructor，表示 Contructor.prototype 是否存在于 object 的原型链上
// object 必须是一个实例对象，否则返回 false，一般用来判断继承关系
function Foo() {} 
const f = new Foo()
f instanceof Foo === true
```

```javascript
// constructor，获取对象的构造函数
(2).constructor === Number
true.constructor === Boolean
''.constructor === String
[].constructor === Array
{}.constructor === Object
(function() {}).constructor === Function
```

```javascript
// 如果要判断引用类型，需要使用 toString()
Object.prototype.toString.call([]) === '[object Array]'
Object.prototype.toString.call(new Date()) === '[object Date]'
Object.prototype.toString.call(function() {}) === '[object Function]'
```

### &&、||、!! 运算符分别能做什么

`&&` 是逻辑与，在其操作数中找到第一个虚值表达式并返回它，如果没有找到任何虚值表达式，则返回最后一个真值表达式。它采用短路来防止不必要的工作。
`||` 是逻辑或，在其操作数中找到第一个真值表达式并返回它。这也使用了短路来防止不必要的工作。在支持 ES6 默认函数参数之前，它用于初始化函数中的默认参数值。
`!!` 运算符可以将右侧的值强制转换为布尔值，这也是将值转换为布尔值的一种简单方法。

### new 关键字都做了什么 ?

### Instanceof 的实现原理是什么 ?

### 讲一讲作用域 ?

作用域是 JS 内部实现的，规定了如何在当前作用域和外层作用域中根据指定的标识符进行变量查找。如果在当前执行上下文中没有找到，就去父级执行上下文中查找，一直找到全局执行上下文中的变量对象，这样的多个执行上下文的变量对象构成的链表就交作用域链。

1. 说一说原型链 ?

2. this 的绑定有哪几种方式 ?

   ```javascript
   // 1. 默认绑定，非严格模式，this 指向 window，严格模式下指向 undefined
   // 2. 隐式绑定，当函数调用存在上下文对象时，谁调用函数 this 指向谁
   // 3. 显示绑定，call / apply / bind 的第一个参数，forEach / map / filter 的第二个参数
   // 4. new，new 会创建一个新对象，并且把这个对象绑定到函数里的 this
   // 5. 箭头函数，this 始终指向外层函数调用者，并且在函数定义时就已经决定了
   ```

   

3. 改变 this 的方法有哪些 ?

   ```javascript
   // call / apply / bind
   // new
   // 箭头函数
   // const obj = {}; function foo() {}; obj.foo = foo;
   ```

   

4. 如何理解闭包 ? 闭包有哪些优点和缺点 ?

5. 闭包，单例模式，模块化的关联理解 ?

6. 讲一讲变量提升，let 和 const 也有变量提升吗？临时死区是什么？

7. 如何理解变量对象和活动对象 ?

8. 如何理解函数执行上下文、函数执行栈，事件中如何运用函数执行栈定位 BUG ?

9. 什么是隐式转换，以及有哪些应用场景 ?

10. 什么是拆箱和装箱 ?

11. 如何判断一个数据的类型 ?

12. call / apply / bind 的使用与区别 ?

13. 如何判断空数组 ?

14. 开发一个公共弹窗，如何保证它一定出现在最顶层 ?

15. 如何理解事件循环 ?

16. 说一说浏览器的事件循环

17. 说一说 Node 的事件循环

18. 从实践角度来理解，函数中传入函数作为参数有什么意义，forEach 比 for 循环好在那里 ?

19. 什么是浅拷贝和深拷贝 ?

    ```javascript
    // 浅拷贝：将所有的可枚举属性从一个或多个源对象拷贝到目标对象（如果属性是基本数据类型会拷贝基本类型的值，如果属性是引用数据类型则会拷贝引用地址），同时返回目标对象
    // 常见的方式：Object.assign、展开语法...、Array.prototype.slice
    // 问题：
    // 1. 改变目标对象的子对象（包括数组对象），同时会改变源对象的数据结构
    // 2. 原始类型会被包装为对象，但是只有字符串的包装对象才可能有自身可枚举类型，boolean/number/symbol 会被忽略
    
    // 深拷贝：会复制源对象所有的属性，并复制属性指向的动态分配的内存，复制前后两个对象互不影响
    // 常见的方式：JSON.parse(JSON.stringify())
    // 问题：深拷贝比浅拷贝速度慢、开销大
    ```

    

20. setTimeout 和 setInterval 的优缺点 ?

21. 如何判断 0.1 + 0.2 与 0.3 相等

22. 如何使用 JS 控制 div 元素在页面移动 ?

23. 对点击事件和移动事件的理解 ?

24. 如何实现拖拽 ?

25. 如何使用原型封装拖拽元素 ?

26. 使用 ES6 的 class 封装拖拽元素 ?

27. 如何实现元素的匀速运动、加速运动等各种运动方式 ?

28. 封装一个 Tab 切换组件

29. 封装一个轮播组件

30. 如果要加入运动各自如何实现 ?

31. 点击一个 input 依次触发的事件是哪些？

    ```javascript
    'onmouseenter'
    'onmousedown'
    'onfocus'
    'onclick'
    ```

32. 说一说设计模式

33. ES6 的 Set 内部实现

34. 事件循环机制，node 和浏览器的事件循环机制区别

35. typescript 有什么好处

36. ES6 特性

37. Promise 内部实现原理

38. 发布订阅模式和观察者模式的异同

39. 如何解决同步调用代码耗时太高的问题

40. symbol 应用

41. CommonJS 和 ES6 中模块引入的区别

    ```
    CommonJS 最初被用于 Node.js，成为 Node.js 模块规范。在 ES6 发布之前，前端为浏览器端也实现了一些模块规范，如 AMD。ES6 发布后，引入了新的 ES6 Module 规范，在语言标准的层面上实现了模块功能。由于浏览器对 ES6 Module 的兼容性不够，日常使用中在 Webpack 中使用 import 和 export 会经过 Babel 转换为 CommonJS 规范。在使用上的差异有：
    1. CommonJS 模块输出的是值的拷贝，ES6 Module 模块输出的是值的引用
    2. CommonJS 模块是运行时加载，ES6 Module 模块是编译时输出接口
    3. CommonJS 是单个值导出，ES6 Module 可以导出多个值
    4. CommonJS 是动态语法可以写在判断里，ES6 Module 是静态语法只能写在顶层
    5. CommonJS 的 this 是当前模块，ES6 Module 的 this 是 undefined
    ```

##### 一段 Javascript 代码是如何执行的

```
JS 引擎遇到一段代码，会创建一个执行栈，然后创建一个全局上下文。这个全局上下文包含 window/global，this，全局变量声明（赋值 undefined）和全局函数声明。
执行上下文创建完成后，JS 引擎进入执行阶段。执行过程中会给变量赋值，遇到函数调用时，会创建该函数的执行上下文，并将它推入执行栈中，这个函数执行上下文中包含 arguments、this、局部变量、局部函数声明，之后的执行阶段和全局执行上下文一样，如果遇到函数调用会创建新的函数执行上下文，并将它推入执行栈。
有一种特殊情况，子函数执行过程中，父函数已经 return 了，这时 JS 引擎会将父函数的执行上下文从执行栈中移除。如果子函数中用到了父函数中的变量，JS 引擎会为子函数上下文创建一个闭包，包含了父函数内部的变量和值，子函数仍能在自己的上下文中访问到父函数的变量。当子函数执行完毕，JS 引擎将子函数执行上下文和闭包一并从执行栈中移除。
```

