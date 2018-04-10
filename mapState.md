# mapState
`源码 : 836~861行`
##mapState
```js
var mapState = normalizeNamespace(function (namespace, states) {
    var res = {};
    normalizeMap(states).forEach(function (ref) {
        var key = ref.key;
        var val = ref.val;

        res[key] = function mappedState() {
            var state = this.$store.state;
            var getters = this.$store.getters;
            if (namespace) {
                var module = getModuleByNamespace(this.$store, 'mapState', namespace);
                if (!module) {
                    return
                }
                state = module.context.state;
                getters = module.context.getters;
            }
            return typeof val === 'function'
                ? val.call(this, state, getters)
                : state[val]
        };
        // mark vuex getter for devtools
        res[key].vuex = true;
    });
    return res
});
```

作用：为组件创建计算属性以返回 Vuex store 中的状态

* 对states中的每一对键值对进行加工(数组即每一项)，返回一个映射对象res
* res中的键是states中映射过来的键，值是states中每个键对应的值得包装器

`过程：`

* 以一个匿名函数 function( namespace , states )作为参数，调用normalizeNamespace,返回该匿名函数的包装器函数，存储在mapState中
* 当调用mapState(...)时：
    * 首先在包装器里加工参数 [normalizeNamespace](./normalizeNamespace.md)
    * 将res设置为一个空对象{}
    * 将states参数作为[normalizeMap](./normalizeMap.md)的参数进行调用，返回的结果是一个数组，该数组中包含的每一项都是一个包含了，'key','val'两个键的对象
    * 遍历这个数组，在每次的遍历中操作当前得到的state，即 ref :
        * 用局部变量key存储ref.key对应的值
        * 用局部变量val存储ref.val对应的值
        * 将key作为res对象的键，并为其赋值一个函数,mappedState
        * 假如调用`res[key]()`的时候，执行过程如下： 
            * 用局部变量state，存储当前实例上的$store的state
            * 用局部变量getters，存储当前实例上的$store的getters
            * 当namespace的值为truly：
                * 根据namespace获取其对应的module [getModuleByNamespace](./getModuleByNamespace.md)
                * 如果未获取到module，则退出
                * 否则，将module局部上下文的state赋值给state局部变量(替换开始存储的根state)，将module局部上下文的getters赋值给getters局部变量(替换开始存储的根getters)
            * 当val是一个function时，返回val的调用(val绑定到this,即当前的vue实例，传递当前上下文的state和getters)结果
            * 否则返回state中val键对应的值
    * 当forEach结束后，返回设置好的res对象


