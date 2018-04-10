# prototypeAccessors
`源码 : 378~388行`
## prototypeAccessors
```js
var prototypeAccessors = {state: {configurable: true}};
    prototypeAccessors.state.get = function () {
        return this._vm._data.$$state
    };
    prototypeAccessors.state.set = function (v) {
        if (process.env.NODE_ENV !== 'production') {
            assert(false, "Use store.replaceState() to explicit replace store state.");
        }
    };
```

作用 ： 配置用来描述state属性的修饰对象

* state的get实际上是代理this._vm（一个用来响应的vue实例）上data里存储的$$state
* state的set实际上时加了拦截，即不能直接用一个新对象去替换现有的state，如果当前是在非生产环境下，会在控制台报错
* [assert](./assert.md)

