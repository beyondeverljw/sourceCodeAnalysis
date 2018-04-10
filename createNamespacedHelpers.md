# createNamespacedHelpers
`源码 : 938~945行`
##createNamespacedHelpers

```js
var createNamespacedHelpers = function (namespace) {
    return ({
        mapState: mapState.bind(null, namespace),
        mapGetters: mapGetters.bind(null, namespace),
        mapMutations: mapMutations.bind(null, namespace),
        mapActions: mapActions.bind(null, namespace)
    });
};
```

作用：创建基于命名空间的组件绑定辅助函数

`过程：`

* 将当mapState,mapGetters,mapMutations,mapActions绑定到指定的上下文下，即namespace对应的模块上下文
* 返回一个包含'mapState','mapGetters','mapMutations','mapActions'键的对象，分别对应各自对namespace的绑定
* [mapState](./mapState.md)
* [mapGetters](./mapGetters.md)
* [mapMutations](./mapMutations.md)
* [mapActions](./mapActions.md)

