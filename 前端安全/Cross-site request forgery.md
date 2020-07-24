# Cross-site request forgery - 跨站请求伪造

> 跨站请求伪造：攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。

一个典型的CSRF攻击有着如下的流程：

- 受害者登录 a.com，并保留了登录凭证（Cookie）
- 攻击者引诱受害者访问了 b.com
- b.com 向 a.com 发送了一个请求：a.com/act=xx。浏览器会默认携带 a.com 的Cookie
- a.com 接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是受害者自己发送的请求
- a.com 以受害者的名义执行了act=xx
- 攻击完成，攻击者在受害者不知情的情况下，冒充受害者，让 a.com 执行了自己定义的操作

## 几种常见的攻击类型

### GET 类型的 CSRF

GET 类型的 CSRF 利用非常简单，只需要一个 HTTP 请求，一般会这样利用：

```html
 ![](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2018b/ff0cdbee.example/withdraw?amount=10000&for=hacker)
```

在受害者访问含有这个img的页面后，浏览器会自动向`http://bank.example/withdraw?account=xiaoming&amount=10000&for=hacker`发出一次HTTP请求。bank.example就会收到包含受害者登录信息的一次跨域请求。

### POST 类型的 CSRF

这种类型的CSRF利用起来通常使用的是一个自动提交的表单，如：

```html
 <form action="http://bank.example/withdraw" method=POST>
    <input type="hidden" name="account" value="xiaoming" />
    <input type="hidden" name="amount" value="10000" />
    <input type="hidden" name="for" value="hacker" />
</form>
<script> document.forms[0].submit(); </script> 
```

访问该页面后，表单会自动提交，相当于模拟用户完成了一次POST操作。

POST类型的攻击通常比GET要求更加严格一点，但仍并不复杂。任何个人网站、博客，被黑客上传页面的网站都有可能是发起攻击的来源，后端接口不能将安全寄托在仅允许POST上面。

### 链接类型的 CSRF

链接类型的CSRF并不常见，比起其他两种用户打开页面就中招的情况，这种需要用户点击链接才会触发。这种类型通常是在论坛中发布的图片中嵌入恶意链接，或者以广告的形式诱导用户中招，攻击者通常会以比较夸张的词语诱骗用户点击，例如：

```html
  <a href="http://test.com/csrf/withdraw.php?amount=1000&for=hacker" taget="_blank">
  重磅消息！！
  <a/>
```

由于之前用户登录了信任的网站A，并且保存登录状态，只要用户主动访问上面的这个PHP页面，则表示攻击成功。

## CSRF 的特点

- 攻击一般发起在第三方网站，而不是被攻击的网站。被攻击的网站无法防止攻击发生。
- 攻击利用受害者在被攻击网站的登录凭证，冒充受害者提交操作；而不是直接窃取数据。
- 整个过程攻击者并不能获取到受害者的登录凭证，仅仅是“冒用”。
- 跨站请求可以用各种方式：图片URL、超链接、CORS、Form 提交等等。部分请求方式可以直接嵌入在第三方论坛、文章中，难以进行追踪。

CSRF 通常是跨域的，因为外域通常更容易被攻击者掌控。但是如果本域下有容易被利用的功能，比如可以发图和链接的论坛和评论区，攻击可以直接在本域下进行，而且这种攻击更加危险。

## 防护策略

CSRF 通常从第三方网站发起，被攻击的网站无法防止攻击发生，只能通过增强自己网站针对 CSRF 的防护能力来提升安全性。

上文中讲了 CSRF 的两个特点：

- CSRF（通常）发生在第三方域名
- CSRF 攻击者不能获取到 Cookie 等信息，只是使用

针对这两点，我们可以专门制定防护策略，如下：

- 阻止不明外域的访问
  - 同源检测
  - Samesite Cookie
- 提交时要求附加本域才能获取的信息
  - CSRF Token
  - 双重 Cookie 验证
- 验证码
  - 针对银行转账、支付等情况，采用验证码/密码确认的方式来杜绝 CSRF

### 同源检测

既然 CSRF 大多来自第三方网站，那么我们就直接禁止外域（或者不受信任的域名）对我们发起请求。

在 HTTP 协议中，每一个异步请求都会携带两个 Header，用于标记来源域名：

- Origin Header
- Referer Header

这两个 Header 在浏览器发起请求时，大多数情况会自动带上，并且不能由前端自定义内容。 服务器可以通过解析这两个 Header 中的域名，确定请求的来源域。

#### 使用 Origin Header 确定来源域名

在部分与CSRF有关的请求中，请求的Header中会携带Origin字段。字段内包含请求的域名（不包含path及query）。

如果Origin存在，那么直接使用Origin中的字段确认来源域名就可以。

但是Origin在以下两种情况下并不存在：

- IE11 同源策略
- 302 重定向

#### 使用 Referer Header 确定来源域名

根据HTTP协议，在HTTP头中有一个字段叫Referer，记录了该HTTP请求的来源地址。 对于Ajax请求，图片和script等资源请求，Referer为发起请求的页面地址。对于页面跳转，Referer为打开页面历史记录的前一个页面地址。因此我们使用Referer中链接的Origin部分可以得知请求的来源域名。

这种方法并非万无一失，Referer的值是由浏览器提供的，虽然HTTP协议上有明确的要求，但是每个浏览器对于Referer的具体实现可能有差别，并不能保证浏览器自身没有安全漏洞。使用验证 Referer 值的方法，就是把安全性都依赖于第三方（即浏览器）来保障，从理论上来讲，这样并不是很安全。在部分情况下，攻击者可以隐藏，甚至修改自己请求的Referer。

### 双重 Cookie 验证

在会话中存储 CSRF Token 比较繁琐，而且不能在通用的拦截上统一处理所有的接口。

那么另一种防御措施是使用双重提交 Cookie。利用 CSRF 攻击不能获取到用户 Cookie 的特点，我们可以要求 Ajax 和表单请求携带一个 Cookie 中的值。

双重 Cookie 采用以下流程：

- 在用户访问网站页面时，向请求域名注入一个Cookie，内容为随机字符串（例如 `csrfcookie=v8g9e4ksfhw`）。
- 在前端向后端发起请求时，取出 Cookie，并添加到 URL 的参数中（接上例 `POST https://www.a.com/comment?csrfcookie=v8g9e4ksfhw`）。
- 后端接口验证 Cookie 中的字段与 URL 参数中的字段是否一致，不一致则拒绝。

此方法相对于 CSRF Token 就简单了许多。可以直接通过前后端拦截的的方法自动化实现。后端校验也更加方便，只需进行请求中字段的对比，而不需要再进行查询和存储 Token。

当然，此方法并没有大规模应用，其在大型网站上的安全性还是没有 CSRF Token 高。

由于任何跨域都会导致前端无法获取 Cookie 中的字段（包括子域名之间），于是发生了如下情况：

- 如果用户访问的网站为`www.a.com`，而后端的api域名为`api.a.com`。那么在`www.a.com`下，前端拿不到`api.a.com`的Cookie，也就无法完成双重Cookie认证。
- 于是这个认证Cookie必须被种在`a.com`下，这样每个子域都可以访问。
- 任何一个子域都可以修改`a.com`下的Cookie。
- 某个子域名存在漏洞被XSS攻击（例如`upload.a.com`）。虽然这个子域下并没有什么值得窃取的信息。但攻击者修改了`a.com`下的Cookie。
- 攻击者可以直接使用自己配置的 Cookie，对 XSS 中招的用户再向`www.a.com`下，发起 CSRF 攻击。

#### 优点：

- 无需使用Session，适用面更广，易于实施。
- Token 储存于客户端中，不会给服务器带来压力。
- 相对于 Token，实施成本更低，可以在前后端统一拦截校验，而不需要一个个接口和页面添加。

#### 缺点：

- Cookie 中增加了额外的字段。
- 如果有其他漏洞（例如 XSS），攻击者可以注入 Cookie，那么该防御方式失效。
- 难以做到子域名的隔离。
- 为了确保 Cookie 传输安全，采用这种防御方式的最好确保用整站 HTTPS 的方式，如果还没切 HTTPS 的使用这种方式也会有风险。

## 参考资料

- [美团技术 - 前端安全系列（二）：如何防止CSRF攻击？](https://tech.meituan.com/2018/10/11/fe-security-csrf.html)

