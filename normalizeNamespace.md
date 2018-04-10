# normalizeNamespace
`源码 : 957~965行`
##normalizeNamespace(fn)
```js
function normalizeNamespace(fn) {
    return function (namespace, map) {
        if (typeof namespace !== 'string') {
            map = namespace;
            namespace = '';
        } else if (namespace.charAt(namespace.length - 1) !== '/') {
            namespace += '/';
        }
        return fn(namespace, map)
    }
}
```

作用：返回一个fn的包装函数，这个包装函数负责加工fn要用到的参数，并返回fn的调用结果。
    这个包装器实质上就是mapState , mapGetters , mapActions

`过程：`

* 返回fn的包装函数
* 包装函数的过程：
    * 当namespace参数不是string类型时，将其赋值给map，其自身设置为空
    * 当namespace参数是string类型时，检查其是否以'/'结尾，如果不是则添加
    * 以加工好的 namespace , map 为参数，返回调用fn的结果

