# Webpack - 概述

## 基础用法

### entry

单入口的 entry 值为字符串，多入口的 entry 值为对象。

Webpack 通过 entry 文件遍历所有的文件依赖关系，然后生成一个依赖树，最后经过 Webpack 编译打包成静态资源文件。

### output



### Loaders

Loader 本身是一个导出为函数的 Javascript 模块，因此在 Webpack 中多个 loaders 的采用 Compose 的方式组合

```javascript
compose = (f, g) => (...args) => f(g(...args))
```

Webpack 只支持 JS 和 JSON，通过 Loaders 提供的功能，Webpack 才能处理其他文件。常用 Loader 如下：

| 名称                     | 描述                                                         |
| ------------------------ | ------------------------------------------------------------ |
| babel-loader             | 转换 ES6+ 等新特性语法                                       |
| @babel/preset-env        | 转换 ES6+                                                    |
| @babel/preset-typescript | 转换 Typescript                                              |
| @babel/preset-react      | 转换 React                                                   |
| postcss-loader           | 扩展 CSS 语法，使用下一代 CSS，可以配合 autoprefixer 插件自动补齐 CSS3 前缀 |
| style-loader             | 把 CSS 代码注入到 JavaScript 中，通过 DOM 操作去加载 CSS     |
| css-loader               | 加载 CSS，支持模块化、压缩、文件导入等特性                   |
| less-loader              | 将 less 转换成 css                                           |
| sass-loader              | 将SCSS/SASS代码转换成CSS                                     |
| file-loader              | 把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件 (处理图片和字体) |
| url-loader               | 与 file-loader 类似，区别是用户可以设置一个阈值，大于阈值会交给 file-loader 处理，小于阈值时返回文件 base64 形式编码 (处理图片和字体) |
| raw-loader               | 将文件以字符串的形式导入                                     |
| thread-loader            | 多进程打包 JS 和 CSS                                         |
| source-map-loader        | 加载额外的 Source Map 文件，以方便断点调试                   |
| svg-inline-loader        | 将压缩后的 SVG 内容注入代码中                                |
| eslint-loader            | 通过 ESLint 检查 JavaScript 代码                             |
| tslint-loader            | 通过 TSLint检查 TypeScript 代码                              |
| mocha-loader             | 加载 Mocha 测试用例的代码                                    |
| coverjs-loader           | 计算测试的覆盖率                                             |
| i18n-loader              | 国际化                                                       |
| cache-loader             | 可以在一些性能开销较大的 Loader 之前添加，目的是将结果缓存到磁盘里 |

#### 特性

- Loader 支持链式传递，一组 Loaders 从右向左执行，右边的 loader 将返回值传递给左边的 loader，最后一个 loader 将返回 Webpack 所需要的 Javascript
- Loader 可以是同步的，也可以是异步的
- Loader 运行在 Node.js 中，并且能够执行任何可能的操作
- Loader 接收查询参数，用于对 loader 传递配置，也能使用 options 对象进行配置
- 除了使用 package.json 中的 main 属性，还能够将普通的 npm 模块导出为 loader，需要在 package.json 中添加 loader 字段
- Loader 能够产生额外的任意文件

### Plugins

用于增强 Webpack 的功能，完成 Loaders 无法完成的事务，如 bundle 文件的优化，资源管理和环境变量注入。它作用于整个构建进程。常见的 Plugins 如下：

| 名称                           | 描述                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| optimization.splitChunks       | 将 chunks 相同的模块代码提取成公共 js                        |
| clean-webpack-plugin           | 清理构建目录                                                 |
| copy-webpack-plugin            | 将文件或文件夹拷贝到构建的输出目录                           |
| html-webpack-plugin            | 创建 HTML 去承载输出的 bundle                                |
| web-webpack-plugin             | 可方便地为单页应用输出 HTML，比 html-webpack-plugin 好用     |
| uglifyjs-webpack-plugin        | 不支持 ES6 压缩 (Webpack4 以前)                              |
| terser-webpack-plugin          | 支持压缩 ES6 (Webpack4)                                      |
| mini-css-extract-plugin        | 分离样式文件，CSS 提取为独立文件，支持按需加载 (替代extract-text-webpack-plugin) |
| zip-webpack-plugin             | 将打包出的资源生成一个 Zip 包                                |
| ignore-plugin                  | 忽略指定文件                                                 |
| webpack-parallel-uglify-plugin | 多进程执行代码压缩，提升构建速度                             |
| serviceworker-webpack-plugin   | 为网页应用增加离线缓存功能                                   |
| module-concatenation-plugin    | 开启 Scope Hoisting，webpack4 mode:production 默认开启       |
| speed-measure-webpack-plugin   | 可以看到每个 Loader 和 Plugin 执行耗时 (整个打包耗时、每个 Plugin 和 Loader 耗时) |
| webpack-bundle-analyzer        | 可视化 Webpack 输出文件的体积 (业务组件、依赖第三方模块)     |

### optimization

见 [Webpack - Optimization]()

## 基本概念

### 文件监听

Webpack 通过轮询判断文件最后的修改时间是否发生变化，某个文件发生变化后不是立即告诉监听者，而是先缓存起来，等待 aggregateTimeout 

### 热更新原理

### 文件指纹

- Hash，和整个项目的构建相关，只要项目文件有修改，整个项目构建的 hash 值就会改变
- ChunkHash，和 Webpack 打包的 chunk 相关，不同的 entry 会生成不同的 chunkhash 值
- ContentHash，根据文件内容来定义

### 代码压缩

多页面通用打包方案

### Source Map

提取页面公共资源

## 进阶使用

### Tree Shaking（摇树优化）

> mode: production 时自动开启

DCE：代码中没有用到的、不会执行到的，以及只影响死变量（只写不读）的，会被『擦除』，不会被打包。

其原理是利用 ES6 的模块特点来实现：（对 ES6 模块进行静态分析，然后在 Ug）

- import 只能作为模块的顶层的语句出现
- import 模块名只能是字符串常量
- import binding 是 immutable 的

### Scope Hoisting

> mode: production 时自动开启

针对闭包代码打包优化，和 Tree Shaking 类似，只支持 ES6 语法，做静态检查。

## 动手实践 - 编写 loader 和 plugin

### Loader

loader-runner - 在不安装 webpack 的情况下调试 loader

loader-utils - 可以获取传入 loader 的 options

#### 返回值

可以通过 this.callback(null, result) 来处理结果，null 可以是个 Error 对象，第二个参数之后的参数都会被当做结果返回值，如

```javascript
loader => this.callback(null, "foobar", 2, 3);
输出 => [ '"foobar"', 2, 3 ]
```

同步 Loader - 通过 this.callback(null, result) 来返回结果

异步 Loader - 通过 this.async(null, result) 来返回结果

#### 抛出异常

> 使用 this.callback 和 this.async 的第一个参数可以返回异常信息

也可以通过 throw new Erro() 的方式抛出错误。

#### 缓存

> this.cacheable() 来开启/关闭缓存，默认开启

注意：

- 缓存条件：loader 的结果在相同的输入下有确认的输出。
- 有依赖的 loader 无法缓存（会失效）

### Plugin

#### 运行环境

- 没有像 Loader 那样的独立运行环境
- 只能在 Webpack 里面运行

#### 创建插件

Webpack 的插件由以下组成：

1. 一个 Javascript 类，原型上定义一个 apply 方法，该方法会得到一个 Compiler 实例，在 apply 内部可以监听 compiler.hooks
2. 指定一个绑定到 webpack 自身的事件钩子
3. 处理 webpack 内部实例的特定数据
4. 功能完成后调用 webpack 提供的回调

apply 方法绑定 webpack 事件钩子的用法：

- v3 - compiler.plugin
- v4 - compiler.hooks



### compiler 钩子

#### 钩子类型

| 名称                     | 描述             |
| ------------------------ | ---------------- |
| SyncHook                 | 同步钩子         |
| SyncBailHook             | 同步熔断钩子     |
| SyncWaterfallHook        | 同步流水钩子     |
| SyncLoopHook             | 同步循环钩子     |
| AsyncParalleHook         | 异步并发钩子     |
| AsyncParallelBailHook    | 异步并发熔断钩子 |
| AsyncSeriesHook          | 异步串行钩子     |
| AsyncSeriesBailHook      | 异步串行熔断钩子 |
| AsyncSeriesWaterfallHook | 异步串行流水钩子 |



#### 常用钩子

| 名称 | 类型 | 描述 |
| ---- | ---- | ---- |
|      |      |      |
|      |      |      |
|      |      |      |
|      |      |      |
|      |      |      |

compilation.assets

