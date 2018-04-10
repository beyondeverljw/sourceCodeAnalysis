# registerAction
`源码 : 749~772行`
## registerAction(store, type, handler, local)
```js
function registerAction(store, type, handler, local) {
    var entry = store._actions[type] || (store._actions[type] = []);
    entry.push(function wrappedActionHandler(payload, cb) {
        var res = handler.call(store, {
            dispatch: local.dispatch,
            commit: local.commit,
            getters: local.getters,
            state: local.state,
            rootGetters: store.getters,
            rootState: store.state
        }, payload, cb);
        if (!isPromise(res)) {
            res = Promise.resolve(res);
        }
        if (store._devtoolHook) {
            return res.catch(function (err) {
                store._devtoolHook.emit('vuex:error', err);
                throw err
            })
        } else {
            return res
        }
    });
}
```

作用：注册一个action

`过程：`

* 用局部变量entry存储store._actions里type对应的actions，如果没有type类型的actions，则为entry赋值一个空数组[]
* 在这个数组的末尾添加当前action的包装器（wrappedActionHandler）,其内部的原理如下 :
    * 将handler绑定在store上进行调用
    * 调用时为其传递三个参数：
        * 第一个为当前action的局部上下文对象，包含局部'dispatch','commit','getters','state',全局的'rootGetters','rootState'键
        * 第二个为payload
        * 第三个为cb
        * 如果handler的调用结果res不是一个promise对象，则调用promise.resolve(res)将其包装成一个promise对象
        * 当store._devtoolHook为truly时，当promise reject后，返回res.catch的调用结果，其内部触发_devtoolHook的'vuex:error'事件
        * 当store._devtollHook为falsely时，则返回res

