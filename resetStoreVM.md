# resetStoreVM
`源码 : 557~607行`
## resetStoreVM(store, state, hot)
```js
function resetStoreVM(store, state, hot) {
    var oldVm = store._vm;

    // bind store public getters
    store.getters = {};
    var wrappedGetters = store._wrappedGetters;
    var computed = {};
    forEachValue(wrappedGetters, function (fn, key) {
        // use computed to leverage its lazy-caching mechanism
        computed[key] = function () {
            return fn(store);
        };
        Object.defineProperty(store.getters, key, {
            get: function () {
                return store._vm[key];
            },
            enumerable: true // for local getters
        });
    });

    // use a Vue instance to store the state tree
    // suppress warnings just in case the user has added
    // some funky global mixins
    var silent = Vue.config.silent;
    Vue.config.silent = true;
    store._vm = new Vue({
        data: {
            $$state: state
        },
        computed: computed
    });
    Vue.config.silent = silent;

    // enable strict mode for new vm
    if (store.strict) {
        enableStrictMode(store);
    }

    if (oldVm) {
        if (hot) {
            // dispatch changes in all subscribed watchers
            // to force getter re-evaluation for hot reloading.
            store._withCommit(function () {
                oldVm._data.$$state = null;
            });
        }
        Vue.nextTick(function () {
            return oldVm.$destroy();
        });
    }
}
```
作用：为store._vm设置一个新的vue实例用来实现vuex中state到getters的响应

`过程：`

* 首先将store._vm缓存到oldVm
* 将store.getters设置为一个空对象（这个对象其实是一个代理对象，代理store._vm中的computed中的键）
* 将store中的所有getters缓存到局部变量wrappedGetters中。
* 将局部变量computed设置为一个空对象{}
* 通过[forEachValue](forEachValue.md)遍历wrappedGetters对象
    * 每次遍历中，将当前遍历到的getter的键作为computed的键，将getter的值加一层包装，包装内部返回getter的值的调用结果，即wrappedGetters到computed的一一映射。
    * 同时建立wrappedGetters到store.getters键的一一映射，并为store.getters中的键设置访问器，其内部返回的是store._vm中的同名属性对应的值
* 用局部变量silent存储Vue.config.silent的值
* 然后将Vue.config.silent值设为true
* new一个Vue实例放在store._vm属性中，此时建立了响应（用Vue实例监视state的变化，并将wrappedGetters中的getter映射到当前实例的计算属性中，store.getters代理了vue实例对象的计算属性）
* 恢复Vue.config.silent之前的值
* 如果store.strict值为truly，则调用 [enableStrictMode](./enableStrictMode.md)
* 当oldVm为truly时，如果hot为truly,则手动更改oldVm._data.$$state为null，给所有订阅者派发更新
* 在下次 DOM 更新循环结束之后执行,完全销毁oldVm

