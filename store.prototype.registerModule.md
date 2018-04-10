# store.prototype.registerModule
`源码 : 482~498行`
## registerModule(path, rawModule, options)
```js
Store.prototype.registerModule = function registerModule(path, rawModule, options) {
    if (options === void 0) options = {};

    if (typeof path === 'string') {
        path = [path];
    }

    if (process.env.NODE_ENV !== 'production') {
        assert(Array.isArray(path), "module path must be a string or an Array.");
        assert(path.length > 0, 'cannot register the root module by using registerModule.');
    }

    this._modules.register(path, rawModule);
    installModule(this, this.state, path, this._modules.get(path), options.preserveState);
    // reset store to update getters...
    resetStoreVM(this, this.state);
};
```

作用：动态注册一个模块（不可以注册根模块）

`过程：`

* 如果options的值等于undefined，则将其设为一个空对象{}
* 如果path的类型为string，则将其数组化
* 如果当前是在非生产环境，则进行相关信息提示
* [this._modules.register](./moduleCollection.md)
* [installModule](./installModule.md)
* [resetStoreVM](./resetStoreVM.md)


