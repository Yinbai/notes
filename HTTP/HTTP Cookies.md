# HTTP Cookies

> Cookie: name=value
>
> Cookie: name=value; name2=value2; name3=value3

## 产生

1. 服务端设置
2. 客户端设置，document.cookie = "name=value; ..."

## Cookie 的有效期

服务器发送到浏览器的 cookie 存在两种存储方式，分为会话期 Cookie 和持久性 Cookie。

### 会话期 Cookie

如果没有设置 Expires 或 Max-age，那么这就是一个会话期 Cookie，Cookie 被存于内存中，当会话结束或者关闭浏览器窗口时将删除 Cookie

### 持久性 Cookie

设置了 Expires 或者 Max-age 的 Cookie 会被存储在本地硬盘，过期会自动删除。

1. Expires

   是一个有效日期（GMT 格式），是一个绝对时间

2. Max-age

   是一个相对时间，单位秒

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



