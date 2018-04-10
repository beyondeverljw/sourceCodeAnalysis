# registerMutation
`源码 : 742~747行`
## registerMutation(store, type, handler, local)
```js
function registerMutation(store, type, handler, local) {
    var entry = store._mutations[type] || (store._mutations[type] = []);
    entry.push(function wrappedMutationHandler(payload) {
        handler.call(store, local.state, payload);
    });
}
```

作用：注册一个mutation

`过程：`

* 获取store中_mutations里指定类型(type)的控制函数句柄的包装器组成的数组，若该数组不存在，则将其设置为一个空数组
* 在该数组尾部添加控制句柄(handle)包装器(wrappedMutationHandler)
* 当commit提交当前注册的mutation时,handler会以store作为上下文，并以local参数指定的局部state，和commit时传递进来的载荷(payload)作为参数，进行调用

