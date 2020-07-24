# Webpack - 文件指纹

文件指纹 hash 有三种生成方式：

1. Hash - 随着整个 Webpack 的构建而变化，一旦项目中有改动，Hash 值也会变化，该 hash 值是通过对 compilation 内所有的内容的计算得来的
2. Chunkhash - 和 Webpack 打包的 chunk 相关，不同的 entry 对应不同的 chunkhash 值，该 hash 值是通过对 chunk 内所有的内容的计算得来的
3. Contenthash - 随着文件内容的变化而改变

## 三种 hash 遭遇的痛点

设定场景如下

| entry | 入口文件 | 入口文件依赖链                                               |
| ----- | -------- | ------------------------------------------------------------ |
| pageA | a.js     | a.less <- a.css common.js <- common.less <- common.css lodash |
| pageB | b.js     | b.less <- b.css common.js <- common.less <- common.css lodash |

### hash 值为 hash 时

```javascript
{
  output: {
    path: path.join(cwd, 'dist'),
    filename: '[name]-[hash].js'
  },
  plugins: [
    new ExtractTextPlugin('[name]-[hash].css'),
  ]
}
```

构建结果：

```bash
Hash: 7ee8fcb953c70a896294
Version: webpack 3.8.1
Time: 6308ms
                         Asset       Size  Chunks                    Chunk Names
 pageB-7ee8fcb953c70a896294.js     525 kB       0  [emitted]  [big]  pageB
 pageA-7ee8fcb953c70a896294.js     525 kB       1  [emitted]  [big]  pageA
pageA-7ee8fcb953c70a896294.css  147 bytes       1  [emitted]         pageA
pageB-7ee8fcb953c70a896294.css  150 bytes       0  [emitted]         pageB
```

**重复执行构建，在内容没有改动的情况下，hash 值也会发生变化。原因在于我们使用了 extract，extract 本身涉及到异步的抽取流程，所以在生成 assets 资源时存在了不确定性（先后顺序），而 updateHash 则对其敏感，所以就出现了如上所说的 hash 异动的情况。**

### hash 值为 chunkhash 时

```javascript
{
	output: {
    path: path.join(cwd, 'dist'),
    filename: '[name]-[chunkhash].js'
  },
  plugins: [
    new ExtractTextPlugin('[name]-[chunkhash].css'),
  ]
}
```

构建结果：

```bash
Hash: 1b432b2e0ea7c80439ff
Version: webpack 3.8.1
Time: 1069ms
                         Asset       Size  Chunks                    Chunk Names
 pageB-58011d1656e7b568204e.js     525 kB       0  [emitted]  [big]  pageB
 pageA-5c744cecf5ed9dd0feaf.js     525 kB       1  [emitted]  [big]  pageA
pageA-5c744cecf5ed9dd0feaf.css  147 bytes       1  [emitted]         pageA
pageB-58011d1656e7b568204e.css  150 bytes       0  [emitted]         pageB
```

**此时可以发现，运行多少次，hash 的异动没有了，每个 entry 拥有了自己独一的 hash 值，细心的你或许会发现此时样式资源的 hash 值和入口脚本保持了一致，这似乎并不符合我们的想法，冥冥之中告诉我们发生了某些坏事情。**

**此时修改  b.css 文件，重新构建后会发现 pageB.css 的 hash 值依然和 pageB.js 的 hash 值一致。**

因为 webpack 中所有文件都会被视为 Javascript 的一部分，...，提取 CSS 时将它从 entry chunk 中拿出，导致 entry chunk 并无改变

### hash 值为 contenthash 时

当 pageA.js 移除 pageA.css 时，webpack 内部维护的 module ID 会发生变化，比如 76 => 75。此时就需要针对 module id 做处理。

## module id

> 要解决 module id 异动带来的影响，有两种思路：
>
> 1. 屏蔽 module id 异动
> 2. 给 module id 确定值