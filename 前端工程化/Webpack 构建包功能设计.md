# Webpack 构建包功能设计

一个通用的 Webpack 构建包需要包含 4 个部分内容：

1. 基础配置 webpack.base.js
2. 开发配置 webpack.dev.js
3. 生产配置 webpack.prod.js
4. 服务端渲染 webpack.ssr.js

## 基础配置

- 资源解析
  - 解析 ES6
    - @babel/preset-env
    - @babel/plugin-transform-runtime
  - 解析 React / Vue
    - @babel/preset-react
  - 解析 Typescript
    - @babel/preset-typescript
  - 解析 CSS
    - css-loader
      - modules: true 支持 CSS Module
      - modules: true 需要配置 localIdentName
  - 解析 Less / Sass
    - less-loader
    - sass-loader / node-sass
  - 解析图片
    - url-loader
    - file-loader
  - 解析字体
    - file-loader
- 样式增强
  - CSS 前缀补齐
  - px => rem
- CSS 提取到单独文件
  - mini-css-extract-plugin.loader
- 目录清理
  - clean-webpack-plugin
- HTML 模板替换
  - html-webpack-plugin
- 多页面打包
- 命令行信息显示优化
- 捕获错误和处理

## 开发配置

- 热更新
  - CSS 热更新
  - JS 热更新
    - webpack-dev-server
    - webpack-dev-middleware + webpack-hot-middleware
- Sourcemap
- ESLint

## 生产配置

- 代码压缩
- 文件指纹
- Tree Shaking
- Scope Hoisting
- 速度优化
  - 基础包 => CDN
- 体积优化
  - 代码分割

## 服务端渲染

- output 的 libraryTarget 设置
- CSS 解析 ignore

