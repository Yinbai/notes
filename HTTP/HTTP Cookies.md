# HTTP Cookies

> Cookie: name=value
>
> Cookie: name=value; name2=value2; name3=value3

## 产生

1. 服务端设置，响应头添加 Set-Cookie 字段
2. 客户端设置，document.cookie = "name=value; ..."

## Cookie 的有效期

服务器发送到浏览器的 cookie 存在两种存储方式，分为会话期 Cookie 和持久性 Cookie。

### 会话期 Cookie

如果没有设置 Expires 和 Max-age，或者只设置了 Max-age 且值为负数，那么这就是一个会话期 Cookie，Cookie 被存于内存中，当会话结束或者关闭浏览器窗口时将删除 Cookie

**注意**，如果 max-age 为 0 会立即删除该 cookie

当为会话性 Cookie 的时候，值保存在客户端内存中，并在用户关闭浏览器时失效。需要注意的是，有些浏览器提供了会话恢复功能，这种情况下即使关闭了浏览器，会话期 Cookie 也会被保留下来，就好像浏览器从来没有关闭一样。

### 持久性 Cookie

设置了 Expires 或者 Max-age 的 Cookie 会被存储在本地硬盘，过期会自动删除。

1. Expires

   是一个有效日期（GMT 格式），是一个绝对时间

2. Max-age

   是一个相对时间，单位秒

持久性 Cookies 会保存在用户的硬盘中，直至过期或者清除 Cookie。这里值得注意的是，设定的日期和时间只与客户端相关，而不是服务端。

## Cookie 的属性

### name/value

用 JavaScript 操作 Cookie 的时候注意对 Value 进行编码处理。

### Expries

Expires 用于设置 Cookie 的过期时间。比如：

```js
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
```

### Max-age

Max-Age 用于设置在 Cookie 失效之前需要经过的秒数。比如：

```js
Set-Cookie: id=a3fWa; Max-Age=604800;
```

Max-Age 可以为正数、负数、甚至是 0。

当 max-Age 属性为正数时，浏览器会将其持久化，即写到对应的 Cookie 文件中。

当 max-Age 属性为负数，则表示该 Cookie 只是一个会话性 Cookie。

当 max-Age 为 0 时，则会立即删除这个 Cookie。

假如 Expires 和 Max-Age 都存在，Max-Age 优先级更高。

### domain

指定 cookie 可以送达的主机名，如果没有设置 domain，默认为当前文档 URL 中的主机部分（不包含子域名）。

### path

指定一个 URL 路径，这个路径必须出现在要请求的资源的路径中才可以发送 Cookie 首部。比如设置 `Path=/docs`，`/docs/Web/` 下的资源会带 Cookie 首部，`/test` 则不会携带 Cookie 首部。

Domain 和 Path 标识共同定义了 Cookie 的作用域：即 Cookie 应该发送给哪些 URL。

### secure

标记为 Secure 的 Cookie 只应通过被HTTPS协议加密过的请求发送给服务端。使用 HTTPS 安全协议，可以保护 Cookie 在浏览器和 Web 服务器间的传输过程中不被窃取和篡改。

### httpOnly

可以防止客户端脚本通过 document.cookie 等方式访问 Cookie，有助于避免 XSS 攻击。

### samesite

可以让 Cookie 在跨站请求时不会被发送，从而可以阻止跨站请求伪造攻击（CSRF）。

samesite 有 3 个值

- strict - 仅允许同站请求携带 cookie
- lax - 允许第三方请求携带 cookie（Chrome 默认值）
- none - 无论是否跨站都会携带 cookie

##### 跨域和跨站

首先要理解的一点就是跨站和跨域是不同的。同站(same-site)/跨站(cross-site)和第一方(first-party)/第三方(third-party)是等价的。但是与浏览器同源策略（SOP）中的同源(same-origin)/跨域(cross-origin)是完全不同的概念。

同源策略的同源是指两个 URL 的协议/主机名/端口一致。例如，[www.taobao.com/pages/](https://www.taobao.com/pages/)，它的协议是 https，主机名是 www.taobao.com，端口是 443。

同源策略作为浏览器的安全基石，其「同源」判断是比较严格的，相对而言，Cookie中的「同站」判断就比较宽松：只要两个 URL 的 eTLD+1 相同即可，不需要考虑协议和端口。其中，eTLD 表示有效顶级域名，注册于 Mozilla 维护的公共后缀列表（Public Suffix List）中，例如，.com、.co.uk、.github.io 等。eTLD+1 则表示，有效顶级域名+二级域名，例如 taobao.com 等。

举几个例子，www.taobao.com 和 www.baidu.com 是跨站，www.a.taobao.com 和 www.b.taobao.com 是同站，a.github.io 和 b.github.io 是跨站。

##### 改变

接下来看下从 None 改成 Lax 到底影响了哪些地方的 Cookies 的发送？直接来一个图表：

| 请求类型  | 实例                            | 以前 | Strict | Lax    | None |
| --------- | ------------------------------- | ---- | ------ | ------ | ---- |
| 链接      | <a href="../">                  | 发送 | 不发送 | 发送   | 发送 |
| 预加载    | <link rel="prerender" href=""/> | 发送 | 不发送 | 发送   | 发送 |
| GET 表单  | <form method="GET">             | 发送 | 不发送 | 发送   | 发送 |
| POST 表单 | <form method="POST">            | 发送 | 不发送 | 不发送 | 发送 |
| iframe    | <iframe src="">                 | 发送 | 不发送 | 不发送 | 发送 |
| Ajax      | $.get()                         | 发送 | 不发送 | 不发送 | 发送 |
| image     | <img src="" />                  | 发送 | 不发送 | 不发送 | 发送 |

## 读取 Cookie

```

Javasciprt 读取 Cookie:
	const cookie = document.cookie
Javascript 设置 Cookie:
	document.cookie = 'name1=hello'
	document.cookie = 'name2=world'
	document.cookie = 'name1=hello;name2=world'
服务端响应头设置 Cookie:
	Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
```

## Cookie 的作用

- 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
- 个性化设置（如用户自定义设置、主题等）
- 浏览器行为追踪（如跟踪分析用户行为等）

## Cookie 的缺点

- 只能存放 4k 的数据
- 同站请求时都会携带 cookie，有些场景是无意义的，如请求图片
- 跨站请求伪造攻击（CSRF 

