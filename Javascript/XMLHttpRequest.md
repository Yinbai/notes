# XMLHttpRequest 对象

> Javascript 中的 XMLHttpRequest 对象用于与服务器进行数据交互，可用来发送和接收服务器数据。所有的现代浏览器都支持XMLHttpRequest 对象。XMLHttpRequest 对象的用途包括：不重新加载页面的情况下更新网页、在页面已加载后从服务器请求和接收数据、向后台服务器发送数据等。



## 创建 XMLHttpRequest 对象

`const xhr = new XMLHttpRequest()`

## XMLHttpRequest 对象属性

### readyState

HTTP 请求的状态。当用 XMLHttpRequest 对象向服务器发起请求时，readyState 的值会从 0（初始化）递增到 4（接收完成）。通常情况下 readyState 的值只会递增不会减小，有两种情况例外：一个请求在处理过程中调用了 open() 或 abort()。readyState 的值在每次递增时会触发 onreadystatechange 事件。readyState 的值和状态描述如下：

| 状态 | 名称          | 描述                                            |
| ---- | ------------- | ----------------------------------------------- |
| 0    | Uninitialized | 初始化                                          |
| 1    | Open          | open() 已调用，但 send() 仍未调用，请求还未发送 |
| 2    | Send          | send() 已调用，发送请求，但未收到响应           |
| 3    | Receving      | 所有响应头接收完毕，开始接收响应主体，但未完成  |
| 4    | Loaded        | 响应已经完全接收                                |



### responseText

接收到的服务器的响应主体，responseText 的值跟随 readyState 的改变而不同：

readyState < 3 时，responseText 为空字符串

readyState = 3 时，responseText 已经接收部分响应数据

readyState = 4 时，responseText 为完整的响应结果



### responseXML

与 responseText 类似，接收到的服务器的响应结果，但解析为 XML 并作为 Document 对象返回，可以使用 W3C DOM 节点树方法和属性对该 XML 文档对象进行检查和解析。



### status

HTTP 请求响应的状态码，如 200，404。当 readyState < 3 时，该属性不存在，读取该属性会抛出异常



### statusText

服务器响应的状态名称，如 status = 200 时 statusText 为 'OK'。当 readyState < 3 时，该属性不存在，读取该属性会抛出异常



## XMLHttpRequest 对象方法

### abort()

重置当前请求的响应，关闭连接并且结束任何未完成的网络活动。
说明：此方法会把把 XMLHttpRequest 对象 readyState 属性值，重置为 0（初始化状态），并且取消所有完成的网络活动。如果请求用的时间较长，而且响应不再必要的时候，可以调用这个方法。



### getAllResponseHeaders()

把 HTTP 响应头作为未解析的字符串返回。
说明：如果 readyState 小于 3，这个方法返回 null。否则，它返回服务器发送的所有 HTTP 响应头。响应头部作为单个的字符串返回，一行一个头部。每行用换行符 "\r\n" 隔开。



### getResponseHeader()

返回指定的 HTTP 响应头的值。其参数是要返回的 HTTP 响应头部的名称。可以使用任何大小写来制定这个头部名字，和响应头部的比较是不区分大小写的。
说明：如果没有接收到这个头部或者 readyState 小于 3 则为空字符串。如果接收到多个有指定名称的头部，这个头部的值被连接起来并返回，使用逗号和空格分隔开各个头部的值。



### open(method, url, async, username, password)

初始化 HTTP 请求参数，例如 URL 和 HTTP 方法，但是并不发送请求。

**参数说明**
**method**：参数是用于请求的 HTTP 方法。值包括 GET、POST 和 HEAD。
**url**：参数是请求的主体。大多数浏览器实施了一个同源安全策略，并且要求这个 URL 与包含脚本的文本具有相同的主机名和端口。
**async**：参数指示请求使用应该异步地执行。如果这个参数是 false，请求是同步的，后续对 send() 的调用将阻塞，直到响应完全接收。如果这个参数是 true 或省略，请求是异步的，且通常需要一个 onreadystatechange 事件句柄。
**username** 和 **password**：这两个参数是可选的，为 url 所需的授权提供认证资格。

**调用说明**
open()方法用于初始化请求参数，初始化的参数将在其后调用的send()方法中使用。
此方法会把readyState设置为1，删除之前指定的所有请求头部，以及之前接收的所有响应头部，并且把 responseText、responseXML、status 以及 statusText 参数设置为它们的默认值。
当 readyState 为 0 的时候（当 XMLHttpRequest 对象刚创建或者 abort() 方法调用后）以及当 readyState 为 4 时（已经接收响应时），调用这个方法是安全的。
除了为send()方法准备请求参数和重置XMLHttpRequest对象以便复用，open()法没有其他行为。
需要特别注意的是，当这个方法调用的时候，是不会打开一个到 Web 服务器的网络连接的。



### send(body)

发送HTTP请求。使用传递给 open() 方法的参数，向web服务器发送HTTP请求。
说明：
如果通过open()指定的 HTTP 方法是 POST或 PUT，body 参数指定了请求体，请求体为一个字符串或者 Document 对象。如果没有请求体的，这个参数就为 null。
这个方法会发送一个HTTP 请求。如果之前没有调用 open()，即：readyState不是 1，send() 抛出一个异常。
HTTP请求由以下几部分组成：
调用open()方法指定的HTTP 方法、URL 以及认证资格（如果有的话）。
调用setRequestHeader()方法时设定的HTTP请求头信息如果有的话）。
传递给这个方法的 body 参数。

HTTP请求过程
HTTP请求发送后，send()会把readyState 设置为2，并触发 onreadystatechange 事件临听。
如果通过open()方法设置async参数为false，这个方法会阻塞并不会返回，直到readyState为 4 并且服务器的响应被完全接收才会相应。如果为 true或者省略此参数时，send() 立即返回，服务器响应会在一个后台线程中处理。
如果服务器响应带有HTTP重定向，send()方法或后台线程自动进行重定向。
当所有的 HTTP 响应头部已经接收，send()或后台线程把 readyState 设置为 3 并触发 onreadystatechange 事件监听。如果响应较长，send() 或后台线程可能会多次在状态 3 中触发 onreadystatechange 事件监听（此特性可以作为一个下载进度指示器）。
最后，当响应完成，send() 或后台线程把 readyState 设置为 4，并最后一次触发事件监听。



### setRequestHeader(name, value)

向一个打开但未发送的请求设置或添加一个 HTTP 请求。
参数说明
name：要设置的HTTP请求头的名称（此参数不能为空白、冒号或换行）。
value：要设置HTTP请求头的值（此参数不能为换行）。

说明
setRequestHeader()方法用于设置HTTP请求的头信息，此方法的设置会被添加到send()方法创建的请求中。
这个方法只有当 readyState 为 1 的时候才能调用。即：在调用了 open() 之后，且在调用 send() 之前调用。
如果指定HTTP头信息已经指定了，这个头信息对应的新值就是：之前指定的值，加上逗号、空白以及这个新指定的值。
setRequestHeader()方法可以用来设置HTTP请求头包括：Authorization、cookie、User-Agent等。
有些请求头部由 XMLHttpRequest 自动设置而不是由这个方法设置，以符合 HTTP 协议。XMLHttpRequest自动设置的请求头有：Host、Connection、Keep-Alive、Accept-charset、Accept-Encoding、If-Modified-Since、If-None-Match、If-Range、Range

## XMLHttpRequest 对象事件

### onreadystatechange

每次readyState属性改变的时，调用的监听事件。当 readyState = 3（所有响应头部都已经接收到。响应体开始接收但未完成）时，此监听可能会被调用多次。