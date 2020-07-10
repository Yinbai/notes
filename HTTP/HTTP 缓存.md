# HTTP 缓存

> HTTP 缓存分为两种，一种是强缓存，一种是协商缓存。主要作用是减少网络请求，加快资源获取速度，缓解服务器的压力，提升用户体验。

## 强缓存

不发送请求，直接从缓存中读取。在 Chrome 中，强缓存又分为 Disk Cache 和 Memory Cache，缓存文件存放的位置由浏览器决定。是否强缓存由 Expires / Cache-Control 和 Pragma 这三个 Header 属性共同控制。

### Expires

响应头，表示改资源的过期时间，值是 GMT 格式的标准时间。浏览器发起请求时，会根据系统时间和 Expires 进行比较，如果系统时间大于 Expires，缓存失效。由于是和系统时间进行比较，因此存在缓存有效期不准确的情况，因此 Expires 是优先级最低的。目前主要是用于不支持 Cache-Control 的场景。

### Cache-Control

Cache-Control 是 HTTP/1.1 中新增的属性，请求头和响应头中都可以设置，常用的属性值有：

1. max-age，单位是秒，表示在距离本次请求/响应时间的 N 秒范围内，都会命中，否则缓存失效。依据响应头的 Date
2. no-cache，不使用强缓存
3. no-store，禁止使用缓存（包括强缓存和协商缓存），每次都像服务器发送最新请求
4. private，中间代理、CDN 不能缓存此响应
5. public，中间代理、CDN 可以缓存此响应
6. must-revalidate，在缓存过期前可以使用，过期后必须向服务器验证

### Pragma

Pragma 只有一个属性值，就是 no-cache，等同于 Cache-Control: max-age=0 | no-cache，表示不使用强缓存，在这三个头部属性中优先级最高。

## 协商缓存

当强缓存失效或者配置了不使用强缓存，并且请求头携带了 If-None-Match 或 If-Modified-Since，这时会去服务器验证是否命中协商缓存。如果命中了缓存，请求状态会返回 304，并且响应头会设置 ETag 或 Last-Modified。

### ETag 和 If-None-Match

ETag 和 If-Nnoe-Match 的值是一串 hash 码，表示资源文件的标识符。当服务器的文件内容变化时，它的 hash 值也会发生改变。通过请求头中的 ETag 和当前文件的 hash 进行对比，如果相等则表示命中协商缓存。

ETag 又有强弱校验之分，如果 hash 值是由 'W/' 开头的字符串，说明此时协商缓存的校验是弱校验，只有服务器上的文件差异达到能够触发 hash 值变更的时候（ETag ），才会去请求资源，否则命中协商缓存。

### Last-Modified 和 If-Modified-Since

它们的值表示文件最后的修改时间，第一次请求资源时，服务器会把资源的最后修改时间放到响应头的 Last-Modified 属性中，后续发起请求时，请求头会带上上一次响应的 Last-Modified 值，并放到请求头的 If-Modified-Since 属性中。服务器根据文件最后修改时间和 If-Modified-Since 的值进行对比，如果相等，则返回 304。

注：

1. 服务端同时设置 etag 和 lastModified 的情况下，必须同时满足条件才会命中协商缓存。
2. 服务端设置 etag 会计算文件 hash，一会消耗服务端计算能力，二是不同的服务端计算 etag 的结果不同

## 应用场景

1. 图片、不常更改的文件等可以通过缓存来提升页面加载速度