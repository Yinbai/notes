# DOM - 事件

## 事件流



## 事件对象



## 事件监听函数



## 自定义事件

自定义事件可以用来实现事件广播、派发

```html
<div id="a">A</div>
<div id="b">B</div>
<div id="c">C</div>
```

```javascript
const customEvent = new Event('custom')

const a = document.getElementById('a')
const b = document.getElementById('b')
const c = document.getElementById('c')

a.addEventListener('click', function (e) {
  console.log(e.target) // <div id="a">A</div>
  b.dispatchEvent(customEvent)
  c.dispatchEvent(customEvent)
})

b.addEventListener('custom', function (e) {
  console.log(e.target) // <div id="b">B</div>
  console.log('b 感知到 a 触发事件')
})

c.addEventListener('custom', function (e) {
  console.log(e.target) // <div id="c">C</div>
  console.log('c 感知到 a 触发事件')
})
```



## 事件委托

事件委托就是通过浏览器事件流的原理来实现的，无论是在捕获阶段还是在冒泡阶段，一个元素的事件，总是会在父节点被监听到

例如，我们需要监听一个列表中的每个元素的点击事件，获取这个元素的内容。如果给每个元素绑定事件监听函数，会写很多重复代码，且绑定事件的开销较大，如果这个列表可以动态增删元素，那么又要给新元素绑定事件监听函数，非常麻烦。针对这种情况，就可以利用事件冒泡或者事件捕获的特性，把事件监听函数绑定到他们的父级元素上，通过一个监听函数来处理事件：

```html
<ul id="parent">
  <li>详情</li>
  <li>详情</li>
  <li>详情</li>
  <li>详情</li>
  <li>详情</li>
</ul>
```

```javascript
const parent = document.getElementById('parent')
parent.addEventListener('click', function(e) {
  // 处理事件
})
```

以上就是一个简单的事件委托，但是在实际项目中，常常不会有这么简单的 HTML 结构，通常会是这样的：

```javascript
<ul id="parent">
  <li data-id=""><span><icon/></span>详情</li>
  <li data-id=""><span><icon/></span>详情</li>
  <li data-id=""><span><icon/></span>详情</li>
  <li data-id=""><span><icon/></span>详情</li>
  <li data-id=""><span><icon/></span>详情</li>
</ul>
```

`li` 元素里有子元素，而我们只需要获取 `li` 上的数据，或者只想获取 `span` 上的数据，这时就需要做一些过滤操作：

```javascript
/** 
 * 事件委托
 * 通过事件流捕获真正需要监听的元素，再通过 call 来给事件处理函数绑定 this 及传递 event 参数
 * @param element 绑定事件监听函数的 DOM
 * @param eventType 事件类型
 * @param selector 真正监听的元素的选择器
 * @param callback 事件处理函数
 */
function eventAgent(element, eventType, selector, callback) {
  element.addEventListener(eventType, function(event) {
    let target = event.target || event.srcElement;
    const selectors = [...element.querySelectorAll(selector)]
    if (selectors.includes(target)) {
      callback.call(target, event)
    }
  })
}
// 使用
const parent = document.getElementById('parent')
eventAgent(parent, 'click', 'span', function(event) {
  // 处理事件
})
```

