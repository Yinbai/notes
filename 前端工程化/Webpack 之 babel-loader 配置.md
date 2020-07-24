# Webpack 之 babel-loader 配置

## babel/preset-env

@babel/preset-env 会根据环境配置，将 ES6+ 代码转换成环境能支持的 ES5 代码

```javascript
// webpack 配置
{
  test: /\.js$/,
  loader: 'babel-loader',
  include: [
    path.resolve(__dirname, '../src')
  ],
  // +++
  options: {
    presets: [
      [
        '@babel/preset-env',
        {
          targets: {
            chrome: '51',
            ie: '9'
          },
          modules: false,
          useBuiltIns: 'entry',
          corejs: 2
        }
      ]
    ]
  }
}
```

useBuiltIns - 可选项有 false(默认)、entry、usage，各选项说明：

false - 默认，babel 不自动导入 polyfill ，你需要手动在项目全局环境中导入

- 缺点：每个语法都去找其对应的 polyfill 很麻烦，直接 `import babel-polyfill` 时等同于 `useBuiltIns: entry`

entry - 需要你在入口文件 `import @babel/polyfill`，它会依据环境设置，仅导入环境不支持的 polyfill

- 优点：只导入环境不支持的 polyfill
- 缺点：需要手动导入 `@babel/polyfill`

usage - 当每个文件里用到需要 polyfill 的特性时，在文件中添加对应的 polyfill ，可以保证每个 polyfill 只 load 一次，缩小生产包体积

- 优点：只导入需要的 polyfill 并且是自动导入
- 缺点：实验中的属性

> *`useBuiltIns` 值不为 `false` 时需要指明 `corejs` 版本，否则会有警告（虽然有默认值 2）*
>
> `corejs: 2` 表示使用 `@babel/preset-env/lib/polyfills/corejs2` 来翻译 / 填充代码

`@babel/preset-env` 使用起来非常方便，但遗憾的是它并不能覆盖所有开发场景，因为它存在两个缺点：

- **重复填充**: `@babel/preset-env` 会填充每一个文件，所以 a.js / b.js 如果同时用到了 Promise，那么翻译后两个文件均存在 Promise 的填充
- **全局污染**: `@babel/preset-env` 会将 `Promise` 翻译成全局变量 `var _Promise`

如果你打包生成的是公共库，就不能仅仅使用 `@babel/preset-env`，因为你不能控制这个包的使用环境，对全局变量的污染或许会制造一些问题。

## transform-runtime

以上两个问题我们可以借助插件 `@babel/plugin-transform-runtime` 来解决

> This is where the @babel/plugin-transform-runtime plugin comes in: all of the helpers will reference the module @babel/runtime to avoid duplication across your compiled output. The runtime will be compiled into your build.

> Another purpose of this transformer is to create a sandboxed environment for your code. If you use @babel/polyfill and the built-ins it provides such as Promise, Set and Map, those will pollute the global scope. While this might be ok for an app or a command line tool, it becomes a problem if your code is a library which you intend to publish for others to use or if you can't exactly control the environment in which your code will run.

1. 安装

   ```bash
   yarn add @babel/plugin-transform-runtime -D // 开发依赖
   yarn add @babel/runtime-corejs2 // 生产依赖
   ```

2. 配置

   ```javascript
   {
     test: /\.js$/,
     loader: 'babel-loader',
     include: [
       path.resolve(__dirname, '../src')
     ],
     options: {
       plugins: [
         [
           '@babel/plugin-transform-runtime',
           {
             'corejs': 2,
             'absoluteRuntime': false,
             'helpers': true,
             'regenerator': true,
             'useESModules': false
           }
         ]
       ]
     }
   }
   ```

   > `'corejs': 2` 表示使用 `@babel/runtime-corejs2` 来翻译 / 填充代码，默认 `false` 表示自己引入 polyfill

   **需要注意的是， `@babel/plugin-transform-runtime` 由于其不污染全局变量的特性，无法翻译对象的类方法，比如 `Array.includes` / `Array.from` 等**

## 如何选择

如果项目是公共库，使用 `@babel/plugin-transform-runtime` ，否则使用 `@babel/preset-env`

## 常用配置

```bash
yarn add babel-loader @babel/core @babel/preset-env @babel/plugin-syntax-dynamic-import -D
```

### 在 .babelrc 中配置

wepack.config.js 文件

```javascript
{
  test: /\.js$/,
  loader: 'babel-loader',
  include: [
    path.resolve(__dirname, '../src')
  ],
  options: {
    cacheDirectory: true
  }
}
```

.babelrc 文件

```javascript
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "modules": false,
        "useBuiltIns": "usage",
        "targets": {
          "chrome": "58"
        },
        "corejs": 2
      }
    ]
  ],
  "plugins": [
    "@babel/plugin-syntax-dynamic-import"
  ]
}
```

### 在 webpack 的 loader 中配置

```bash
yarn add babel-loader @babel/core @babel/plugin-transform-runtime @babel/plugin-syntax-dynamic-import -D
yarn add @babel/runtime-corejs2
```

```javascript
{
  test: /\.js$/,
  loader: 'babel-loader',
  include: [
    path.resolve(__dirname, '../src')
  ],
  options: {
    cacheDirectory: true,
    presets: [
      [
        '@babel/preset-env',
        {
          modules: false,
          useBuiltIns: 'usage',
          targets: {
            chrome: '58'
          },
          corejs: 2
        }
      ]
    ],
    plugins: [
      '@babel/plugin-syntax-dynamic-import'
    ]
  }
}
```

