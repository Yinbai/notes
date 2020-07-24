# Webpack SSR - React

步骤：

1. 将访问的入口页面通过 react-dom/render 提供的 renderToString 方法来转化成字符串
2. 将字符串放入 HTML 模板字符串中，然后返回整个字符串

打包过程中存在的问题：

1. Node.js 环境中没有 window, document 等对象，如果在服务端渲染时被使用，需要进行 Hack 处理

   - 组件适配 - 将不兼容的组件根据打包环境进行适配
   - 请求适配 - 将 fetch / 或 ajax 发送请求的写法改成 axios 或 isomorphic-fetch
   - 事件适配

2. 样式问题（Node.js 无法解析 CSS）

   - **方案一**：（最优解）服务端打包通过 ignore-loader 忽略掉 CSS 的解析，然后将打包出来的浏览器端的 HTML 作为模板，设置[占位符]，动态插入组件。可支持 CSS 初次渲染和向浏览器端导入 React initialState
   - 方案二：将 style-loader 替换成 isomorphic-style-loader（此方案需要使用 CSS-in-JS，会改变编码习惯）

   

