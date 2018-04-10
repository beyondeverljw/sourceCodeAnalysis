# resetStore
`源码 : 545~555行`
## resetStore(store, hot)
```js
function resetStore(store, hot) {
    store._actions = Object.create(null);
    store._mutations = Object.create(null);
    store._wrappedGetters = Object.create(null);
    store._modulesNamespaceMap = Object.create(null);
    var state = store.state;
    // init all modules
    installModule(store, state, [], store._modules.root, true);
    // reset vm
    resetStoreVM(store, state, hot);
}
```

作用：重置store

`过程：`

* 将store._actions设置为一个干净的空对象
* 将store._mutations设置为一个干净的空对象
* 将store._wrappedGetters设置为一个干净的空对象
* 将store._modulesNamespaceMap设置为一个干净的空对象
* [installModule](./installModule.md)
* [resetStoreVM](./resetStoreVM.md)


