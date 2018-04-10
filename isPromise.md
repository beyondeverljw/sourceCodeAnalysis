# isPromise
`源码97~99行`
##isPromise( val )
```js
function isPromise(val) {
    return val && typeof val.then === 'function'
}
```

作用：判断一个对象是否是一个promise

`过程：`

* 首先判断val是否为truly,如果不是直接返回false
* 如果是，则继续判断它是否为对象，且有一个then方法
* 如果val是对象，且有一个then方法，则认为其实一个promise

