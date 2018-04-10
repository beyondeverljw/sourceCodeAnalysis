# getModuleByNamespace
`源码 : 969~975行`
##getModuleByNamespace(store, helper, namespace)
```js
function getModuleByNamespace(store, helper, namespace) {
    var module = store._modulesNamespaceMap[namespace];
    if (process.env.NODE_ENV !== 'production' && !module) {
        console.error(("[vuex] module namespace not found in " + helper + "(): " + namespace));
    }
    return module
}
```

作用：根据指定的namespace找到对应的module，并返回

`过程：`

* 从_modulesNamespaceMap中获取namespace指定的module
* 如果module不存在，且当前环境是非生产环境,则在控制台进行错误提示
* 返回找到的module


