# Store.prototype.unregisterModule 
`源码 : 500~517行`
## unregisterModule(path)
```js
Store.prototype.unregisterModule = function unregisterModule(path) {
    var this$1 = this;

    if (typeof path === 'string') {
        path = [path];
    }

    if (process.env.NODE_ENV !== 'production') {
        assert(Array.isArray(path), "module path must be a string or an Array.");
    }

    this._modules.unregister(path);
    this._withCommit(function () {
        var parentState = getNestedState(this$1.state, path.slice(0, -1));
        Vue.delete(parentState, path[path.length - 1]);
    });
    resetStore(this);
};
```

过程：动态注销一个模块

`过程：`

* 将this存储在局部变量this$1中
* 如果path的类型是一个Sting，则将其数组化
* 如果当前运行在非生产环境，进行相关信息提示
* 调用 [this._modules.unregister](./moduleCollection.md)注销
* 用局部变量parentState存储要注销的模块的最近父级模块
* 通过Vue.delete删除当前模块的相关响应
* [resetStore](./resetStore.md)


