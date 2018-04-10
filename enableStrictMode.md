# enableStrictMode
`源码 : 791~799`
## enableStrictMode(store)
```js
function enableStrictMode(store) {
    store._vm.$watch(function () {
        return this._data.$$state
    }, function () {
        if (process.env.NODE_ENV !== 'production') {
            assert(store._committing, "Do not mutate vuex store state outside mutation handlers.");
        }
    }, {deep: true, sync: true});
}
```

作用：启用严格模式，即不可以在mutation之外直接修改state

`过程：`

* 通过vue实例的`$watch`深度监视`$$state`
* 当$$state内部状态发生改变的时候，当store._committing为falsely,抛出异常
* [assert](./assert.md)


