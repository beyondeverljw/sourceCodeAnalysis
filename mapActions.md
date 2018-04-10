# mapActions
`源码 : 912~936行`
#mapActions#
```js
var mapActions = normalizeNamespace(function (namespace, actions) {
    var res = {};
    normalizeMap(actions).forEach(function (ref) {
        var key = ref.key;
        var val = ref.val;

        res[key] = function mappedAction() {
            var args = [], len = arguments.length;
            while (len--) args[len] = arguments[len];

            var dispatch = this.$store.dispatch;
            if (namespace) {
                var module = getModuleByNamespace(this.$store, 'mapActions', namespace);
                if (!module) {
                    return
                }
                dispatch = module.context.dispatch;
            }
            return typeof val === 'function'
                ? val.apply(this, [dispatch].concat(args))
                : dispatch.apply(this.$store, [val].concat(args))
        };
    });
    return res
});
```

作用：创建组件方法分发 action

* 对actions中的每一对键值对进行加工(数组即每一项)，返回一个映射对象res
* res中的键是actions中映射过来的键，值是actions中每个键对应的值得包装器

`过程：`

* 以一个匿名函数 function( namespace , states )作为参数，调用normalizeNamespace,返回该参数的包装器函数，存储在mapActions中
* <a href="./normalizeNamespace.html"></a>
* 当调用mapActions(...)时：
    * 首先在包装器里加工参数 [normalizeNamespace](./normalizeNamespace.md)
    * 将res设置为一个空对象{}
    * 首先，将actions参数作为normalizeMap的参数进行调用，返回的结果是一个数组，该数组中包含的每一项都是一个包含了，'key','val'两个键的对象
    * 遍历这个数组，在每次的遍历中操作当前得到的action，即 ref :
        * 用局部变量key存储ref.key对应的值
        * 用局部变量val存储ref.val对应的值
        * 将key作为res对象的键，并为其赋值一个函数,mappedAction
        * 假如调用res`[key]()`的时候，执行过程如下： 
            * 将arguments中的每一项放入arg中
            * 用dispatch局部变量存储当前vue实例中$store里的dispatch
            * 当namespace的值为truly，根据namespace获取其对应的module
            * [getModuleByNamespace](./getModuleByNamespace.md)
            * 如果未获取到module，则退出
            * 否则，将module局部上下文的dispatch赋值给dispatch局部变量(替换开始存储的根dispatch)
            * 当val是一个function时，返回val的调用(val绑定到this,即当前的实例，传递当前上下文的dispatch和args)结果
            * 否则，将dispatch绑定到当前实例的$store上，给其传递当前val和args作为参数，进行调用，并返回结果
    * 当forEach结束后，返回设置好的res对象

