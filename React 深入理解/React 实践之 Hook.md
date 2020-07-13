# React 实践之 Hooks

1. 每次调用 Hook 都会获取独立的 state

2. [ count, setCount ] = useState(0)

   count 和 setCount 成对出现，useState() 的参数表示 count 的初始值

   如果有多个 state，就要写多个 useState()

2. 更新 state 操作是替换而不像 class 那样合并

3. 传递给 useEffect 的函数在每次渲染后都是不同的

4. 如果 useEffect 返回值是一个函数，React 将在组件卸载时执行

5. useEffect 的清除阶段在每次渲染后都会执行，而不是 class 那样在 willUnmount 才执行

6. useEffect 在组件每次渲染后都会执行清理或执行 Effect 可能会导致性能问题，可以通过传入第二个参数来告诉 Effect 根据哪些字段来作对比，如果两次渲染之间它们没有改变，就跳过 Effect。这是一个数组，元素是字段名。

   注：如果数组中有多个字段，只要有一个字段在两次重渲染之间发生变化，那么久会调用 Effect

7. React 会将上一次渲染后的的值和这次渲染前的值进行比对，然后决定在重渲染后是否会调用 Effect。那么问题来了，useEffect 肯定是被存放于某个地方，等待 React 判断需要调用时才会被执行





Hooks 的优势

1. useEffect 可以更方便的订阅事件监听和取消事件监听，代码更直观
2. useEffect 可以专注做一件事，使得代码逻辑更紧凑，多个不相关的逻辑可以放在不同的 useEffect 中



Hooks 的不便

1. 如果有多个不相关的 state，useState 要写多个