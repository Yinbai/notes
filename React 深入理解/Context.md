# Context

> React 的数据是通过 props 属性自上而下层层传递，而在某种场景下传递数据(比如地区数据)是及其繁琐的。Context 提供了一组在组件之间共享数据的方式，而不是通过逐层传递 props。

## 何时使用 Context

> Context 主要应用场景在于*很多*不同层级的组件需要访问同样一些的数据。请谨慎使用，因为这会使得组件的复用性变差。

有的时候在组件树中很多不同层级的组件需要访问同样的一批数据。Context 能让你将这些数据向组件树下所有的组件进行“广播”，所有的组件都能访问到这些数据，也能访问到后续的数据更新。使用 context 的通用的场景包括管理当前的 locale，theme，或者一些缓存数据。

## Context API

1. **React.createContext**

   ```react
   const AreaContext = React.createContext(defaultValue);
   ```

   创建一个 Context 对象。当 React 渲染一个订阅了这个 Context 对象的组件，这个组件会从组件树中离自身最近的那个匹配的 `Provider` 中读取到当前的 context 值。

   只有当组件所处的树中没有匹配到 Provider 时，其 `defaultValue` 参数**才**会生效。这有助于在不使用 Provider 包装组件的情况下对组件进行测试。注意：将 `undefined` 传递给 Provider 时，消费组件的 `defaultValue` 不会生效。

   

2. **Context.Provider**

   ```react
   <AreaContext.Provider value={areadata}>
     <Toolbar />
   </AreaContext.Provider>
   ```

   每个 Context 对象都会返回一个 Provider React 组件，它允许消费组件订阅 context 的变化。

   Provider 接收一个 `value` 属性，传递给消费组件。一个 Provider 可以和多个消费组件有对应关系。多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。

   当 Provider 的 `value` 值发生变化时，它内部的所有消费组件都会重新渲染。Provider 及其内部 consumer 组件都不受制于 `shouldComponentUpdate` 函数，因此当 consumer 组件在其祖先组件退出更新的情况下也能更新。

   通过新旧值检测来确定变化，使用了与 [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description) 相同的算法。

   > 注意
   >
   > 当传递对象给 `value` 时，检测变化的方式会导致一些问题：详见[注意事项](https://react.docschina.org/docs/context.html#caveats)。

   

3. **Class.contextType**

   ```react
   export default class Test extends React.Component {
     componentDidMount() {
       const value = this.context;
       /* 在组件挂载完成后，使用 AreaContext 组件的值来执行一些有副作用的操作 */
     }
     componentDidUpdate() {
       const value = this.context;
       /* ... */
     }
     componentWillUnmount() {
       const value = this.context;
       /* ... */
     }
     render() {
   	 const areadata = this.context;
       /* ... */
     }
   }
   ```

   挂载在 class 上的 `contextType` 属性会被重赋值为一个由 [`React.createContext()`](https://react.docschina.org/docs/context.html#reactcreatecontext) 创建的 Context 对象。这能让你使用 `this.context` 来消费最近 Context 上的那个值。你可以在任何生命周期中访问到它，包括 render 函数中。

   > 注意：
   >
   > 你只通过该 API 订阅单一 context。如果你想订阅多个，阅读[使用多个 Context](https://react.docschina.org/docs/context.html#consuming-multiple-contexts) 章节
   >
   > 如果你正在使用实验性的 [public class fields 语法](https://babeljs.io/docs/plugins/transform-class-properties/)，你可以使用 `static` 这个类属性来初始化你的 `contextType`。



4. **Context.Consumer**

   ```react
   <AreaContext.Consumer>
     {value => /* 基于 context 值进行渲染*/}
   </AreaContext.Consumer>
   ```

   这里，React 组件也可以订阅到 context 变更。这能让你在[函数式组件](https://react.docschina.org/docs/components-and-props.html#function-and-class-components)中完成订阅 context。

   这需要[函数作为子元素（function as a child）](https://react.docschina.org/docs/render-props.html#using-props-other-than-render)这种做法。这个函数接收当前的 context 值，返回一个 React 节点。传递给函数的 `value` 值等同于往上组件树离这个 context 最近的 Provider 提供的 `value` 值。如果没有对应的 Provider，`value` 参数等同于传递给 `createContext()`的 `defaultValue`。

   > 注意
   >
   > 想要了解更多关于“函数作为子元素（function as a child）”模式，详见 [render props](https://react.docschina.org/docs/render-props.html)。



## 示例

### 单一组件

`areaContext.js`

``` javascript
import React from 'react';

export const areadata = [{
	value: "110000",
	label: "北京市",
	children: [{
		value: "110100",
		label: "市辖区",
		children: [{
				value: "110101",
				label: "东城区"
			}
		]
	}]
}];

export const AreaContext = React.createContext(areadata);
```

`App.js`

```react
import { areadata, AreaContext } from './constant/areadata';

function Toolbar(props) {
	return (
		<Test></Test>
	)
}

function App() {
  return (
    <div className="App">
			<AreaContext.Provider value={areadata}>
				<Toolbar />
			</AreaContext.Provider>
    </div>
  );
}
```

`Test.js`

``` react
import React from 'react';
import { AreaContext } from '../constant/areadata';

export default class Test extends React.Component {
  render() {
		const areadata = this.context;

    return (
			<div>
				{areadata.map(item => <span>{item.label}</span>)}
			</div>
		)
  }
}

Test.contextType = AreaContext;
```



### 嵌套组件

``` react
// Theme context，默认的 theme 是 “light” 值
const ThemeContext = React.createContext('light');

// 用户登录 context
const UserContext = React.createContext({
  name: 'Guest',
});

class App extends React.Component {
  render() {
    const {signedInUser, theme} = this.props;

    // 提供初始 context 值的 App 组件
    return (
      <ThemeContext.Provider value={theme}>
        <UserContext.Provider value={signedInUser}>
          <Layout />
        </UserContext.Provider>
      </ThemeContext.Provider>
    );
  }
}

// 一个组件可能会消费多个 context
function Content() {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <UserContext.Consumer>
          {user => (
            <ProfilePage user={user} theme={theme} />
          )}
        </UserContext.Consumer>
      )}
    </ThemeContext.Consumer>
  );
}
```

## 其他传递共享数据的方式

### 组合组件

> 将子组件自身作为 props 传递，适用于只有某个简单的子组件依赖顶层组件的数据的场景
>
> 缺点：
>
> 这种将逻辑提升到组件树的更高层次来处理，会使得这些高层组件变得更复杂，并且会强行将低层组件适应这样的形式

```javascript
function Page(props) {
  const user = props.user;
  const userLink = (
    <Link href={user.permalink}>
      <Avatar user={user} size={props.avatarSize} />
    </Link>
  );
  return <PageLayout userLink={userLink} />;
}

// 现在，我们有这样的组件：
<Page user={user} avatarSize={avatarSize} />
// ... 渲染出 ...
<PageLayout userLink={...} />
// ... 渲染出 ...
<NavigationBar userLink={...} />
// ... 渲染出 ...
{props.userLink}
```

