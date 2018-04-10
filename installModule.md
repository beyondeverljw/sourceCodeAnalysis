# installModule
`源码 : 609~648行`
## installModule(store, rootState, path, module, hot)
```js
function installModule(store, rootState, path, module, hot) {
    var isRoot = !path.length;
    var namespace = store._modules.getNamespace(path);

    // register in namespace map
    if (module.namespaced) {
        store._modulesNamespaceMap[namespace] = module;
    }

    // set state
    if (!isRoot && !hot) {
        var parentState = getNestedState(rootState, path.slice(0, -1));
        var moduleName = path[path.length - 1];
        store._withCommit(function () {
            Vue.set(parentState, moduleName, module.state);
        });
    }

    var local = module.context = makeLocalContext(store, namespace, path);

    module.forEachMutation(function (mutation, key) {
        var namespacedType = namespace + key;
        registerMutation(store, namespacedType, mutation, local);
    });

    module.forEachAction(function (action, key) {
        var type = action.root ? key : namespace + key;
        var handler = action.handler || action;
        registerAction(store, type, handler, local);
    });

    module.forEachGetter(function (getter, key) {
        var namespacedType = namespace + key;
        registerGetter(store, namespacedType, getter, local);
    });

    module.forEachChild(function (child, key) {
        installModule(store, rootState, path.concat(key), child, hot);
    });
}
```

作用：安装模块，即将要安装的模块及其子模块的的相关 state , actions , mutations , getters 进行注册

`过程：`

* 用局部变量isRoot标识当前path指向的模块是否为根模块。（当path.length===0时，即根模块）
* 将path数组转换成namespace，即转换成字符串表示形式(比如：'shop/boos/vue/')
* 如果module.namespaced的值为truly，则意味着该模块为局部上下文
    * 则将该module，赋值到store._modulesNamespaceMap的namespace属性上，即namespace与module一一对应。
* 如果module不是根模块，且hot值为falsely
    * 则将module的最近一级父模块的state存储到parentState，将当前module的名字存储到moduleName
    * 通过Vue.set将moduleName对应的state设置成响应的
* 通过[makeLocalContext](./makeLocalContext.md)为namespace对应的module设置局部上下文，并存储到module.context和local局部变量中
* 遍历 [module](./module.md)的mutations,在每次遍历中:
* 通过调用 [registerMutation](./registerMutation.md)注册当前模块中的mutation。即添加mutation到store的_mutations[namespaceType]中
* 遍历 [module](./module.md)的actions，在每次遍历中通过调用 [registerAction](./registerAction.md)注册当前模块中的action。即添加action到store的_actions[type]中
* 遍历 [module](./module.md)的getters，在每次遍历中通过调用 [registerGetter](./registerGetter.md)注册当前模块中的getter。即添加getter到store的_wrappedGetters[namespacedType]中
* 遍历module的子模块配置，在每次遍历中递归调用installModule进行相关的注册。


