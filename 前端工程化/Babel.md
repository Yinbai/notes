# Babel

前期准备，初始化一个项目，在 src 下新建 index.js 文件，package.json 的 scripts 中添加命令

```javascript
// index.js
const foo = () => {
	console.log('foo')
}
```

```json
// package.json
{
	"scripts": {
		"compiler": "babel src --out-dir dist --watch"
	}
}
```

## 核心

@babel/core，这是 babel 的核心包。

## CLI

@babel/cli，package.json 中的命令就是通过它来执行的。

## 预设(presets)

官方 presets 有这些：

- @babel/preset-env
- @babel/preset-flow
- @babel/preset-react
- @babel/preset-typescript

### babel/preset-env

`@babel/preset-env` 主要作用是对我们所使用的并且目标浏览器中缺失的功能进行代码转换和加载 `polyfill`，在不进行任何配置的情况下，`@babel/preset-env` 所包含的插件将支持所有最新的JS特性(ES2015,ES2016等，不包含 stage 阶段)，将其转换成ES5代码。

例如，代码中使用了可选链(目前，仍在 stage 阶段)，那么只配置 `@babel/preset-env`，转换时会抛出错误，需要另外安装相应的插件。

```javascript
// .babelrc
{
  "presets": ["@babel/preset-env"]
}
```

需要说明的是，`@babel/preset-env` 会根据配置的目标环境，生成插件列表来编译。对于基于浏览器或 `Electron` 的项目，官方推荐使用 `.browserslistrc` 文件来指定目标环境。默认情况下，如果没有在 `Babel` 配置文件中(如 .babelrc)设置 `targets` 或 `ignoreBrowserslistConfig`，`@babel/preset-env` 会使用 `browserslist` 配置源。

如果不是要兼容所有的浏览器和环境，推荐配置目标环境，这样编译的代码能够保持最小。

例如，仅包括浏览器市场份额超过 0.25％ 的用户所需的 `polyfill` 和代码转换（忽略没有安全更新的浏览器，如 IE10 和 BlackBerry）:

```json
//.browserslistrc
> 0.25%
not dead
```

查看 `browserslist` 的[更多配置](https://github.com/browserslist/browserslist)

例如，将 `.browserslistrc` 的内容配置为: 

```json
last 2 Chrome versions
```

然后再执行 `npm run compiler`，你会发现箭头函数不会被编译成ES5，因为 `chrome` 的最新2个版本都能够支持箭头函数。

### polyfill

仅仅配置 @babel/preset-env 的情况下，修改 src/index.js

```javascript
// index.js
const isHas = [1, 2, 3].includes(2);

const p = new Promise((resolve, reject) => {
  resolve(100);
});
```

执行 npm run compiler 后，编译出的结果是：

```javascript
const isHas = [1, 2, 3].includes(2);
const p = new Promise(function (resolve, reject) {
  resolve(100);
});
```

低版本浏览器中是没有 Array.prototype.includes 和 Promise 的，这样不能满足兼容低版本浏览器的需求。因为 @babel/preset-env 只是将高版本的语法转换成低版本的，并没有为低版本浏览器添加缺失的新特性。这时就需要 polyfill 了，polyfill 的意思是垫片，可以理解为垫片不同环境下 Javascript 的差异。

`@babel/polyfill` 模块包括 `core-js` 和一个自定义的 `regenerator runtime` 模块，可以模拟完整的 ES2015+ 环境（不包含第4阶段前的提议）。

这意味着可以使用诸如 `Promise` 和 `WeakMap` 之类的新的内置组件、 `Array.from` 或 `Object.assign` 之类的静态方法、`Array.prototype.includes` 之类的实例方法以及生成器函数(前提是使用了 `@babel/plugin-transform-regenerator` 插件)。为了添加这些功能，`polyfill` 将添加到全局范围和类似 `String` 这样的内置原型中(会对全局环境造成污染，后面我们会介绍不污染全局环境的方法)。

**注**：v7.4.0 版本开始，`@babel/polyfill` 已经被废弃(前端发展日新月异)，需单独安装 `core-js` 和 `regenerator-runtime` 模块。

首先，安装 `@babel/polyfill` 依赖: 

```shell
npm install --save @babel/polyfill
```

注意：不使用 `--save-dev`，因为这是一个需要在源码之前运行的垫片。

我们需要将完整的 `polyfill` 在代码之前加载，修改我们的 `src/index.js`: 

```js
import '@babel/polyfill';

const isHas = [1,2,3].includes(2);

const p = new Promise((resolve, reject) => {
    resolve(100);
});
```

`@babel/polyfill` 需要在其它代码之前引入，我们也可以在 `webpack` 中进行配置。

例如:

```javascript
entry: [
    require.resolve('./polyfills'),
    path.resolve('./index')
]
```

`polyfills.js` 文件内容如下:

```js
//当然，还可能有一些其它的 polyfill，例如 stage 4 之前的一些 polyfill
import '@babel/polyfill';
```

现在，我们的代码不管在低版本还是高版本浏览器(或 node 环境)中都能正常运行了。不过，很多时候，我们未必需要完整的 `@babel/polyfill`，这会导致我们最终构建出的包的体积增大，`@babel/polyfill`的包大小为89K (当前 `@babel/polyfill` 版本为 7.7.0)。

我们更期望的是，如果我使用了某个新特性，再引入对应的 `polyfill`，避免引入无用的代码。

`@babel/preset-env` 提供了一个 `useBuiltIns` 参数，设置值为 `usage` 时，就只会包含代码需要的 `polyfill` 。有一点需要注意：配置此参数的值为 `usage` ，必须要同时设置 `corejs` (如果不设置，会给出警告，默认使用的是"corejs": 2) ，注意: 这里仍然需要安装 `@babel/polyfill`(当前 `@babel/polyfill` 版本默认会安装 "corejs": 2):

> 首先说一下使用 `core-js@3` 的原因，`core-js@2` 分支中已经不会再添加新特性，新特性都会添加到 `core-js@3`。例如你使用了 `Array.prototype.flat()`，如果你使用的是 `core-js@2`，那么其不包含此新特性。为了可以使用更多的新特性，建议使用 `core-js@3`。

安装依赖：

```shell
npm install --save core-js@3
```

现在，修改 `Babel` 的配置文件如下:

```javascript
//.babelrc
const presets = [
    [
        "@babel/env",
        {   
            "useBuiltIns": "usage",
            "corejs": 3
        }
    ]
]
```

`Babel` 会检查所有代码，以便查找在目标环境中缺失的功能，然后仅仅把需要的 `polyfill` 包含进来。

例如，`src/index.js` 代码不变：

```js
const isHas = [1,2,3].includes(2);

const p = new Promise((resolve, reject) => {
    resolve(100);
});
```

我们看看编译出来的文件(`dist/index`): 

```js
"use strict";

require("core-js/modules/es.array.includes");

require("core-js/modules/es.object.to-string");

require("core-js/modules/es.promise");

var isHas = [1, 2, 3].includes(2);
var p = new Promise(function (resolve, reject) {
    resolve(100);
});
```

同样的代码，我们用 `webpack` 构建一下(`production` 模式)，能看到最终的代码大小仅为: 20KB。而如果我们引入整个 `@babel/polyfill` 的话，构建出的包大小为：89KB

前面曾提到，在 `useBuiltIns` 参数值为 `usage` 时，仍然需要安装 `@babel/polyfill`，虽然我们上面的代码转换中看起来并没有使用到，但是，如果我们源码中使用到了 `async/await`，那么编译出来的代码需要 `require("regenerator-runtime/runtime")`，在 `@babel/polyfill` 的依赖中，当然啦，你也可以只安装 `regenerator-runtime/runtime` 取代安装 `@babel/polyfill`。

### @babel/plugin-transform-runtime

`Babel` 会使用很小的辅助函数来实现类似 `_createClass` 等公共方法。默认情况下，它将被添加(`inject`)到需要它的每个文件中。

假如，我们的 `src/index.js` 是这样的:

```js
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    };
    getX() {
        return this.x;
    }
}

let cp = new ColorPoint(25, 8);
```

编译出来的 `lib/index.js`，如下所示:

```js
"use strict";

require("core-js/modules/es.object.define-property");

function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

function _defineProperties(target, props) { for (var i = 0; i < props.length; i++) { var descriptor = props[i]; descriptor.enumerable = descriptor.enumerable || false; descriptor.configurable = true; if ("value" in descriptor) descriptor.writable = true; Object.defineProperty(target, descriptor.key, descriptor); } }

function _createClass(Constructor, protoProps, staticProps) { if (protoProps) _defineProperties(Constructor.prototype, protoProps); if (staticProps) _defineProperties(Constructor, staticProps); return Constructor; }

var Point = /*#__PURE__*/function () {
  function Point(x, y) {
    _classCallCheck(this, Point);

    this.x = x;
    this.y = y;
  }

  _createClass(Point, [{
    key: "getX",
    value: function getX() {
      return this.x;
    }
  }]);

  return Point;
}();

var cp = new ColorPoint(25, 8);
```

看起来，似乎并没有什么问题，但是你想一下，如果你有10个文件中都使用了这个 `class`，是不是意味着 `_classCallCheck`、`_defineProperties`、`_createClass` 这些方法被 `inject` 了10次。这显然会导致包体积增大，最关键的是，我们并不需要它 `inject` 多次。

这个时候，就是 `@babel/plugin-transform-runtime` 插件大显身手的时候了，使用 `@babel/plugin-transform-runtime` 插件，所有帮助程序都将引用模块 `@babel/runtime`，这样就可以避免编译后的代码中出现重复的帮助程序，有效减少包体积。

`@babel/plugin-transform-runtime` 是一个可以重复使用 `Babel` 注入的帮助程序，以节省代码大小的插件。

> 注意：诸如 `Array.prototype.flat()` 等实例方法将不起作用，因为这需要修改现有的内置函数(可以使用 `@babel/polyfill` 来解决这个问题) ， 对此需要说明的是如果你配置的是`corejs3`， **`core-js@3` 现在已经支持原型方法，同时不污染原型**。

另外，`@babel/plugin-transform-runtime` 需要和 `@babel/runtime` 配合使用。

首先安装依赖，`@babel/plugin-transform-runtime` 通常仅在开发时使用，但是运行时最终代码需要依赖 `@babel/runtime`，所以 `@babel/runtime` 必须要作为生产依赖被安装，如下 :

```shell
npm install --save-dev @babel/plugin-transform-runtime
npm install --save @babel/runtime
```

除了前文所说的，`@babel/plugin-transform-runtime` 可以减少编译后代码的体积外，我们使用它还有一个好处，它可以为代码创建一个沙盒环境，如果使用 `@babel/polyfill` 及其提供的内置程序（例如 `Promise` ，`Set` 和 `Map` ），则它们将污染全局范围。虽然这对于应用程序或命令行工具可能是可以的，但是如果你的代码是要发布供他人使用的库，或者无法完全控制代码运行的环境，则将成为一个问题。

`@babel/plugin-transform-runtime` 会将这些内置别名作为 `core-js` 的别名，因此您可以无缝使用它们，而无需 polyfill。

修改 `.babelrc` 的配置，如下:

```json
//.babelrc
{
    "presets": [
        [
            "@babel/preset-env",
            {
                "useBuiltIns": "usage",
                "corejs": 3
            }
        ]
    ],
    "plugins": [
        [
            "@babel/plugin-transform-runtime"
        ]
    ]
}
```

重新执行 `npm run compiler` ，编译出来的内容为：

```js
"use strict";

var _interopRequireDefault = require("@babel/runtime/helpers/interopRequireDefault");

var _classCallCheck2 = _interopRequireDefault(require("@babel/runtime/helpers/classCallCheck"));

var _createClass2 = _interopRequireDefault(require("@babel/runtime/helpers/createClass"));

var Point = /*#__PURE__*/function () {
  function Point(x, y) {
    (0, _classCallCheck2["default"])(this, Point);
    this.x = x;
    this.y = y;
  }

  (0, _createClass2["default"])(Point, [{
    key: "getX",
    value: function getX() {
      return this.x;
    }
  }]);
  return Point;
}();

var cp = new ColorPoint(25, 8);
```

可以看出，帮助函数现在不是直接被 `inject` 到代码中，而是从 `@babel/runtime` 中引入。前文说了使用 `@babel/plugin-transform-runtime` 可以避免全局污染，我们来看看是如何避免污染的。

首先新增依赖 `@babel/runtime-corejs3`:

```shell
npm install @babel/runtime-corejs3 --save
```

然后需要移除 `@babel/preset-env` 的 `useBuiltIns` 的配置，不然就重复了，接下来修改配置文件：

```js
// .babelrc
{
    "presets": [
        [
            "@babel/preset-env"
        ]
    ],
    "plugins": [
        [
            "@babel/plugin-transform-runtime",{
                "corejs": 3
            }
        ]
    ]
}
```

然后重新编译，看一下，编译出来的结果

```js
"use strict";

var _interopRequireDefault = require("@babel/runtime-corejs3/helpers/interopRequireDefault");

var _promise = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/promise"));

var _includes = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/instance/includes"));

var _context;

var isHas = (0, _includes["default"])(_context = [1, 2, 3]).call(_context, 2);
var p = new _promise["default"](function (resolve, reject) {
  resolve(100);
});
```

可以看出，没有直接去修改 `Array.prototype`，或者是新增 `Promise` 方法，避免了全局污染。如果上面 `@babel/plugin-transform-runtime` 配置的 `core-js` 是 "2"，其中不包含实例的 `polyfill` 需要单独引入。

> 划重点：如果我们配置的 `corejs` 是 `3` 版本，那么不管是实例方法还是全局方法，都不会再污染全局环境。

看到这里，不知道大家有没有这样一个疑问？给 `@babel/plugin-transform-runtime` 配置 `corejs` 是如此的完美，既可以将帮助函数变成引用的形式，又可以动态引入 `polyfill`，并且不会污染全局环境。何必要给 `@babel/preset-env` 提供 `useBuiltIns` 功能呢，看起来似乎不需要呀。

Why ?

