# makeLocalGetters
`源码 : 715~740行`
## makeLocalGetters(store, namespace)
```js
function makeLocalGetters(store, namespace) {
    var gettersProxy = {};
    var splitPos = namespace.length;
    Object.keys(store.getters).forEach(function (type) {
        // skip if the target getter is not match this namespace
        if (type.slice(0, splitPos) !== namespace) {
            return
        }
        // extract local getter type
        var localType = type.slice(splitPos);
        // Add a port to the getters proxy.
        // Define as getter property because
        // we do not want to evaluate the getters in this time.
        Object.defineProperty(gettersProxy, localType, {
            get: function () {
                return store.getters[type];
            },
            enumerable: true
        });
    });
    return gettersProxy
}
```

作用：将当前namespace下的所有getter代理到一个对象上，并返回这个对象

`过程：`

* 将局部变量gettersProxy设置为一个空对象{}
* 用局部变量splitPos存储namespace的长度
* 获取store.getters中所有键组成的数组，并遍历它，在每次遍历中
    * 获取到type(即getters中每个getter的键)前splitPos位，看其是否与namespace值相等(即是否匹配指定的路径)
    * 如果不相等，则跳过本次迭代
    * 用局部变量localType,存储当前getter的键（不包括前面的路径 ）,例如,对于namespace为'shop/books/'，type为'shop/books/vue'则此时localType等于'vue'
    * 为gettersProxy添加localType属性，并为其设置获取器
* 返回代理对象



