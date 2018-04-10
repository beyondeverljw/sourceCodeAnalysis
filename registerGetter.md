# registerGetter
`源码 : 774~789`
## registerGetter(store, type, rawGetter, local)
```js
function registerGetter(store, type, rawGetter, local) {
    if (store._wrappedGetters[type]) {
        if (process.env.NODE_ENV !== 'production') {
            console.error(("[vuex] duplicate getter key: " + type));
        }
        return
    }
    store._wrappedGetters[type] = function wrappedGetter(store) {
        return rawGetter(
            local.state, // local state
            local.getters, // local getters
            store.state, // root state
            store.getters // root getters
        )
    };
}
```

作用: 注册一个getter

`过程：`

* 首先判断store的_wrappedGetters里是否有指定type的getter，如果有则退出
* 如果是在非生产环境里，会在控制台进行错误提示
* 在store的_wrappedGetters里指定type类型的getter包装器(wrappedGetter)
* 当该包装器被调用的时候，会返回rawGetter的调用结果，rawGetter被调用时会为其传递四个参数，分别是：
    * 局部的state，局部的getters，全局的state，全局的getters


