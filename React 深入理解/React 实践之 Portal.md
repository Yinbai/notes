# React Portal

> Portal 提供了一种将子节点渲染到父组件以外的 DOM 节点的方案

### API

``` react
ReactDOM.createPortal(child, container);
```

`child` 是任何可渲染的 React 子元素，如一个元素、字符串、Fragment

`container` 是一个 DOM 元素



### Usage

```react
render() {
  return ReactDOM.createPortal(this.props.children, node);
}
```



> Portal 的典型用例是当父组件有 overflow: hidden; 或 z-index 样式时，可以使子组件"跳出"父容器，例如对话框、悬浮卡、提示框



### Other

1. Portal 组件仅仅是在 DOM 中"跳出"了父容器，Portal 组件仍然存在于 React 树中，和在 DOM 树中的位置无关，因此其他行为和普通的 React 组件的行为一致，如 context、事件冒泡等

