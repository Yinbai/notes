# useRef

```js
const ref = useRef(initialValue);
```

接收一个初始值，返回一个可变的 ref 对象，其 .current 属性被初始化为 initialValue。ref 对象在组件的整个生命周期内保持不变。

一个常见的用例便是命令式地访问子组件：

```react
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

本质上，`useRef` 就像是可以在其 `.current` 属性中保存一个可变值的“盒子”。[注：这句话如何理解 ？]

如果你将 ref 对象以 `<div ref={myRef} />` 形式传入组件，则无论该节点如何改变，React 都会将 ref 对象的 `.current` 属性设置为相应的 DOM 节点。

然而，`useRef()` 比 `ref` 属性更有用。它可以[很方便地保存任何可变值](https://zh-hans.reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables)，其类似于在 class 中使用实例字段的方式。`useRef` 会在每次渲染时返回同一个 ref 对象。

变更 `.current` 属性不会引发组件重新渲染。如果想要在 React 绑定或解绑 DOM 节点的 ref 时运行某些代码，则需要使用[回调 ref](https://zh-hans.reactjs.org/docs/hooks-faq.html#how-can-i-measure-a-dom-node) 来实现。

## useImperativeHandle

```js
useImperativeHandle(ref, createHandle, [deps])
```

`useImperativeHandle` 可以让你在使用 `ref` 时自定义暴露给父组件的实例值。在大多数情况下，应当避免使用 ref 这样的命令式代码。`useImperativeHandle` 应当与 [`forwardRef`](https://zh-hans.reactjs.org/docs/react-api.html#reactforwardref) 一起使用：



```react
// 定义一个 Input 组件
const Input = (props, ref) => {
  // input 的 ref
  const refInput = useRef();
	// 自定义暴露给父组件的实例值，在封装公共组件时特别有用，防止外部通过 ref 调用组件实例
  useImperativeHandle(
    ref,
    () => ({
      // 此处只暴露一个 focus 方法，外部调用 Input 的 ref 对象时，在 ref.current 上只会有 focus 实例方法
      focus: () => {
        refInput.current.focus();
      },
    }),
    []
  );
  
  return <input ref={refInput} onChange={(e) => props.onChange(e.target.value)} />;
};
```

```react
// 外部调用 Input 组件
export default () => {
  const r1 = useRef();
  const r2 = useRef();

  // 通过 forwardRef 来装饰 Input，这样 Input 组件可以收到除 props 之外的第二个参数 ref
  const MInput = forwardRef(Input);

  return (
    <>
      <MInput ref={r1} onChange={(x) => console.log('1:', x, r1)} />
      <button onClick={() => r1.current.focus()}>focus 1</button>
      <MInput ref={r2} onChange={(x) => console.log('2:', x)} />
      <button onClick={() => r2.current.focus()}>focus 2</button>
    </>
  );
};
```

