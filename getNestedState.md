# getNestedState
`源码 : 801~807行`
##getNestedState(state, path)
```js
function getNestedState(state, path) {
    return path.length
        ? path.reduce(function (state, key) {
            return state[key];
        }, state)
        : state
}
```

作用：获取path指向的state

`过程：`

* 如果path数组的长度为0，则直接返回state
* 否则，以path的每一项作为key，从state开始一直找到path最后一项对应的state


