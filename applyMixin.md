#applyMixin
`源码：6~40行`
##applyMixin(Vue) 

```js
var applyMixin = function (Vue) {
    var version = Number(Vue.version.split('.')[0]);

    if (version >= 2) {
        Vue.mixin({beforeCreate: vuexInit});
    } else {
        // override init and inject vuex init procedure
        // for 1.x backwards compatibility.
        var _init = Vue.prototype._init;
        Vue.prototype._init = function (options) {
            if (options === void 0) options = {};

            options.init = options.init
                ? [vuexInit].concat(options.init)
                : vuexInit;
            _init.call(this, options);
        };
    }

    /**
     * Vuex init hook, injected into each instances init hooks list.
     */

    function vuexInit() {
        var options = this.$options;
        // store injection
        if (options.store) {
            this.$store = typeof options.store === 'function'
                ? options.store()
                : options.store;
        } else if (options.parent && options.parent.$store) {
            this.$store = options.parent.$store;
        }
    }
};

```

作用：为每一个Vue实例注入vuexInit的钩子，在vue2.0+通过全局混入{ beforeCreate: vuexInit }


`vuexInit 的过程`

* a.首先获取当前Vue实例的$options，并将其存储到options局部变量中
* b.如果在options中存在store的配置(比如当前Vue组件是根组件)，则判断其类型：
 	* b1.如果是函数类型，则调用，将返回的结果存放到当前实例的$store属性中
	* 如果非函数，则直接赋值给当前实例的$store属性
* 如果在options中不存在store的配置(比如当前Vue组件是子组件)，则尝试从options中读取parent,及parent.$store
* 如能读取到，则将其赋值当前实例的$store属性








