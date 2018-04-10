# Store.prototype.watch
`源码 : 463~472行`
## watch(getter, cb, options)
```js
Store.prototype.watch = function watch(getter, cb, options) {
    var this$1 = this;

    if (process.env.NODE_ENV !== 'production') {
        assert(typeof getter === 'function', "store.watch only accepts a function.");
    }
    return this._watcherVM.$watch(function () {
        return getter(this$1.state, this$1.getters);
    }, cb, options)
};
```

作用：利用Vue实例(_watcherVM)监视getter函数的返回值,当该返回值发生变化的时候，调用cb

`过程：`

* 将this存储在局部变量this$1中
* 当getter的类型不是function，如果在非生产环境中，则通过 [assert](./assert.md)抛出异常
* 通过this._watcherVM的$watch监测匿名函数的返回值，_watchVM是一个Vue实例


