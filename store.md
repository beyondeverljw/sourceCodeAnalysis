# Store构造函数
`源码：305~376行`
## Store
```js
var Store = function Store(options) {
    var this$1 = this;
    if (options === void 0) options = {};

    // Auto install if it is not done yet and `window` has `Vue`.
    // To allow users to avoid auto-installation in some cases,
    // this code should be placed here. See #731
    if (!Vue && typeof window !== 'undefined' && window.Vue) {
        install(window.Vue);
    }

    if (process.env.NODE_ENV !== 'production') {
        assert(Vue, "must call Vue.use(Vuex) before creating a store instance.");
        assert(typeof Promise !== 'undefined', "vuex requires a Promise polyfill in this browser.");
        assert(this instanceof Store, "Store must be called with the new operator.");
    }

    var plugins = options.plugins;
    if (plugins === void 0) plugins = [];
    var strict = options.strict;
    if (strict === void 0) strict = false;

    var state = options.state;
    if (state === void 0) state = {};
    if (typeof state === 'function') {
        state = state() || {};
    }

    // store internal state
    this._committing = false;
    this._actions = Object.create(null);
    this._actionSubscribers = [];
    this._mutations = Object.create(null);
    this._wrappedGetters = Object.create(null);
    this._modules = new ModuleCollection(options);
    this._modulesNamespaceMap = Object.create(null);
    this._subscribers = [];
    this._watcherVM = new Vue();

    // bind commit and dispatch to self
    var store = this;
    var ref = this;
    var dispatch = ref.dispatch;
    var commit = ref.commit;
    this.dispatch = function boundDispatch(type, payload) {
        return dispatch.call(store, type, payload)
    };
    this.commit = function boundCommit(type, payload, options) {
        return commit.call(store, type, payload, options)
    };

    // strict mode
    this.strict = strict;

    // init root module.
    // this also recursively registers all sub-modules
    // and collects all module getters inside this._wrappedGetters
    installModule(this, state, [], this._modules.root);

    // initialize the store vm, which is responsible for the reactivity
    // (also registers _wrappedGetters as computed properties)
    resetStoreVM(this, state);

    // apply plugins
    plugins.forEach(function (plugin) {
        return plugin(this$1);
    });

    if (Vue.config.devtools) {
        devtoolPlugin(this);
    }
};
```

作用：构造函数，当调用 new Vuex.Store时就是调用这个构造函数。

`过程：`

* 首先用局部变量this$1存储this，即当前实例
* 如果options未定义，即等于undefined，那么将其初始化为一个空对象{}
* 如果Vue的值为falsely，且是在浏览器(window)环境下，并且window.Vue为truly
    * 则调用 [install](./install.md)安装vuex(实质是全局混入beforeCreate : vuexInit)
* 如果当前是在非生产环境，则进行相关信息提示
* 将options.plugins赋值给局部变量plugins，如果其值等于undefined，则将其设为一个空数组[]
* 将options.strict赋值给局部变量strict，如果其值等于undefined，则将其设为false,及当前模块默认非严格模式
* 将options.state赋值给局部变量state，如果其值等于undefined,则将其设为一个空对象{}
* 当state的类型是一个function,则调用，如果其调用结果为truly，则将其赋值给局部变量state，否则将一个空对象{}赋值给局部变量state
* this._committing : 用于标识严格模式下是否可以从外部修改store._vm中的$$state
* this._actions : 用于存储所有actions
* this._actionSubscribers : 用于存储action的订阅者
* this._mutations : 用于存储所有mutations
* this._wrappedGetters : 用于存储所有getters
* this._modules : 一个 [ModuleCollection](./moduleCollection.md)类的实例,负责管理由[module](./module.md)节点组成的一棵树。
* this._modulesNamespaceMap : 用于存储所有模块及其对应的字符串路径的映射
* this._subscribers: 用于存储mutation的订阅者
* this._watcherVM:一个vue实例，借用其$watch实现观测一个function的返回结果
* this.dispatch为一个包装器，在其内部通过将原型上的dispatch绑定到当前实例上条用，并作为返回值（主要是为了保证this的指向）
* this.commit为一个包装器，在其内部通过将原型上的commit绑定到当前实例上条用，并作为返回值（主要是为了保证this的指向）
* 将局部变量strict赋值给当前实例的strict属性
* 调用 [installModule](./installModule.md)递归安装模块
* 调用 [resetStoreVM](./resetStoreVM.md)重置状态
* 遍历plugins，并在每次遍历中以当前实例作为参数，并进行plugin的调用


