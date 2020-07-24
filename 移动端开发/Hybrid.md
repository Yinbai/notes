# Hybrid

## Bridge

定义 Native 和 H5 之间的通信，H5 通过一个固定的桥对象可以访问 Native，反过来 Native 也可以通过它访问 H5。

JS 调用 Native 常用的两种方式：

1. 拦截 URL Scheme。H5 通过 location.href 或者 iframe.src 跳转至类似 ewap:// 这样的一个 URL Scheme，Native 的 WebView 拦截 URL Scheme 和对应的参数，执行一些操作后将数据组装成 JSON，然后通过 JSBridge 通知 H5 回调

2. Native 注入 API。基于 WebView 提供的能力，Native 可以向 Window 注入对象和方法。JS 通过调用这个对象或方法，就可以直接调用 Native 的方法，执行对应的逻辑操作。这种方式，JS 需要等到 Native 执行完相应的逻辑才能执行回调。Android 的 WebView 有 addJavascriptInterface 方法，IOS 的 WKWebView 有 window.webkit.messageHandlers 方法。

   JS 调用时，需要传入方法名，参数，回调函数作为字符串传递（需要注册在 window 上，Native 可以访问）

Native 调用 JS 的方式：

1. 直接可以调用暴露在 Window 上的方法

## Webview

基于 webkit 引擎，可以解析 DOM，展示 HTML 页面的 Native 控件，可以当成浏览器看待。

Android 有 Webview，iOS 有 WKWebView。