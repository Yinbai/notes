# Webpack - Optimization

> 打包优化主要用于多页面应用，多所有页面加载的依赖进行合理打包。

首先，构建多页应用，往往需要抽离多个 chunk 包

1. common - 将同时被多个页面引入的依赖包打入一个 common chunk 中，可以根据被引用的次数或者被引用的 pages 数量来决定哪些依赖需要被打包
2. dll - 将每个页面都会依赖且基本不会改动的依赖打入 dll chunk 中，如 react / react-dom，这样可以防止其他模块的变动污染了 dll 的 hash 缓存
3. manifest - webpack 运行时 （runtime）代码，每当依赖包发生变化，webpack 的运行时代码也会发生变化，如果不将这部分抽离出来，就可能会影响 common 包的 hash 值
4. 页面入口文件对应的文件

然后我们会给打出的 chunk 包名，注入 contentHash，以实现最大缓存效果。在我们分 chunk 的过程中，最关键的一个思想就是，每次迭代发布，尽量减少 chunk hash 值的改变。

如下代码：

```javascript
const commonOptions = {
  chunks: 'all',
  reuseExistingChunk: true
}

export default {
  namedChunks: true,
  moduleIds: 'hashed',
  runtimeChunk: {
    name: 'manifest'
  },
  splitChunks: {
    maxInitialRequests: 5,
    cacheGroups: {
      polyfill: {
        test: /[\\/]node_modules[\\/](core-js|raf|@babel|babel)[\\/]/,
        name: 'polyfill',
        priority: 2,
        ...commonOptions
      },
      dll: {
        test: /[\\/]node_modules[\\/](react|react-dom)[\\/]/,
        name: 'dll',
        priority: 1,
        ...commonOptions
      },
      commons: {
        name: 'commons',
        minChunks: Math.ceil(pages.length / 3), // 至少被1/3页面的引入才打入common包
        ...commonOptions
      }
    }
  }
}
```

### runtimeChunk

在 webpack4 之前，抽离 manifest，需要使用 CommonsChunkPlugin，配置一个指定 name 属性为 'manifest' 的 chunk。在 webpack4 中，无需手动引入插件，配置 runtimeChunk 即可。

### splitChunks

这个配置能让我们以一定规则抽离想要的包，我们可能会抽好几个包，如 vendor + common，所以 splitChunks 中提供 cacheGroups 字段，cacheGroups 每增加一个 key，就相当于多一个抽包规则。

抽离 dll 包的方式有两种：

1. 在网上很多教程中，dll 往往是专门再加一个 webpack 配置，使用 DllPlugin 来构建 dll 库，再在自己项目工程的 webpack 中利用 DllReferencePlugin 来映射 dll 库。虽然这样构建速度会快不少，但是，哎，是真 TM 烦.....
2. 在 webpack4 中利用 splitChunks，配好规则，再抽离对应的 dll 包

除了 dll 与 common 两个 chunk，还需要增加一个 polyfill。这是因为我们用的某些新的库或者使用某些 ES 6+ 语法（如 async/await）需要 runtime 垫片。比如我工程中使用了 react 16，需要增加 `Map`/`Set`/`requestAnimationFrame` ([reactjs.org/docs/javasc…](https://reactjs.org/docs/javascript-environment-requirements.html))。那我必须在 dll 库加载之前增加 polyfill，因此我将所有 core-js 与 babel 引入的包专门打进 polyfill，保证后续加载的 chunk 能执行。`priority`字段用来配置 chunk 的引入优先级，一般的项目应该都是 polyfill > dll > common > page。

splitChunks 中配置项`maxInitialRequests`表示在一个入口(entry)中，最大初始请求 chunk 数(不包含按需加载的，即 dom 中 script 引入的 chunk)，默认值是 3。我现在 cacheGroups 中已经有三个，又因为配置了 runtimeChunk，会打出 manifest，故而总共有 4 个 chunk 包，超出了默认 3 个，因此需要重新配置值。

### moduleIds

稍微了解过 webpack 运行机制的同学会知道，项目工程中加载的 module，webpack 会为其分配一个 moduleId，映射对应的模块。这样产生的问题是一旦工程中模块有增删或者顺序变化，moduleId 就会发生变化，进而可能影响所有 chunk 的 content hash 值。只是因为 moduleId 变化就导致缓存失效，这肯定不是我们想要的结果。

在 webpack4 以前，通过 `HashedModuleIdsPlugin` 插件，我们可以将模块的路径映射成 hash 值，来替代 moduleId，因为模块路径是基本不变的，故而 hash 值也基本不变。

但在 webpack4 中，只需要`optimization`的配置项中设置 `moduleIds` 为 `hashed` 即可。

### namedChunks

除了 moduleId，我们知道分离出的 chunk 也有其 chunkId。同样的，chunkId 也有因其 chunkId 发生变化而导致缓存失效的问题。由于`manifest`与打出的 chunk 包中有`chunkId`相关数据，所以一旦如“增删页面”这样的操作导致 chunkId 发生变化，可能会影响很多的 chunk 缓存失效。

在 webpack4 以前，通过增加`NamedChunksPlugin`，使用 chunkName 来替换 chunkId，实现固化 chunkId，保持缓存的能力。在 webpack4 中，只需在`optimization`的配置项中设置 `namedChunks` 为 `true` 即可。

### css 相关

在 webpack4 以前，使用 `extract-text-webpack-plugin` 插件将 css 从 js 包中分离出来单独打包。在 webpack 中则需要换成 `MiniCssExtractPlugin`。并且在生产环境或者需要 HMR（模块热替换）时，要用 `MiniCssExtractPlugin.loader` 替换 `style-loader`。

注意，这里有个坑。由于开发环境我们会配置热更新，css 的热更新目前`MiniCssExtractPlugin.loader`自身还待支持，故而还需要增加 `css-hot-loader`。 **切记，`css-hot-loader`一定不能在生产环境下使用。否则每次构建过程所有 js chunk 包的 contentHash 值都会不一致，进而导致所有 js 缓存失效。** 因为生产环境增加这个配置不会有任何报错，页面也能正常构建，故而容易忽视。

