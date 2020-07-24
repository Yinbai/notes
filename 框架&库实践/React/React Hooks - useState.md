# useState

模拟一个 useState

```js
let _state;
function useState(initialState) {
  _state = _state || initialState;
  
	function setState(nextState) {
    _state = nextState;
    render();
  }
  return [_state, setState];
}
```

