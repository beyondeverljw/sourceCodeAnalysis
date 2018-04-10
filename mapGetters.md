# mapGetters
`源码 : 889~910行`
##mapGetters
```js
var mapGetters = normalizeNamespace(function (namespace, getters) {
    var res = {};
    normalizeMap(getters).forEach(function (ref) {
        var key = ref.key;
        var val = ref.val;

        val = namespace + val;
        res[key] = function mappedGetter() {
            if (namespace && !getModuleByNamespace(this.$store, 'mapGetters', namespace)) {
                return
            }
            if (process.env.NODE_ENV !== 'production' && !(val in this.$store.getters)) {
                console.error(("[vuex] unknown getter: " + val));
                return
            }
            return this.$store.getters[val]
        };
        // mark vuex getter for devtools
        res[key].vuex = true;
    });
    return res
});
```

作用：为组件创建计算属性以返回 getter 的返回值

* 对getters中的每一对键值对进行加工(数组即每一项)，返回一个映射对象res
* res中的键是getters中映射过来的键，值是getters中每个键对应的值得包装器

`过程：`

* 以一个匿名函数 function( namespace , states )作为参数，调用normalizeNamespace,返回该参数的包装器函数，存储在mapGetters中
* 当调用mapGetters(...)时：
    * 首先在包装器里加工参数 [normalizeNamespace](./normalizeNamespace.md)
    * 将res设置为一个空对象{}
    * 将getters参数作为normalizeMap的参数进行调用，返回的结果是一个数组，该数组中包含的每一项都是一个包含了，'key','val'两个键的对象
    * 遍历这个数组，在每次的遍历中操作当前得到的getter，即 ref :
        * 用局部变量key存储ref.key对应的值
        * 用局部变量val存储ref.val对应的值
        * 给val加上指定的namespace
        * 将key作为res对象的键，并为其赋值一个函数,mappedGetter
        * 当调用`res[key]()`时，执行过程如下：
            * 当namespace为truly时,且根据namespace的指向找不到相应的模块时候，则退出。
            * [getModuleByNamespace](./getModuleByNamespace.md)
            * 如果当前是非生产环境，且val不在当前实例的$store.getters中，则在控制台进行错误提示
            * 返回当前实例中$store.getters中val对应的值
    * 当forEach结束后，返回设置好的res对象 

