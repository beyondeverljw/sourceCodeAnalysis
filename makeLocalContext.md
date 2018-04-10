# makeLocalContext
`源码 : 650~713行`
## makeLocalContext(store, namespace, path)
```js
/**
 * make localized dispatch, commit, getters and state
 * if there is no namespace, just use root ones
 */
function makeLocalContext(store, namespace, path) {
    var noNamespace = namespace === '';

    var local = {
        dispatch: noNamespace ? store.dispatch : function (_type, _payload, _options) {
            var args = unifyObjectStyle(_type, _payload, _options);
            var payload = args.payload;
            var options = args.options;
            var type = args.type;

            if (!options || !options.root) {
                type = namespace + type;
                if (process.env.NODE_ENV !== 'production' && !store._actions[type]) {
                    console.error(("[vuex] unknown local action type: " + (args.type) + ", global type: " + type));
                    return
                }
            }

            return store.dispatch(type, payload)
        },

        commit: noNamespace ? store.commit : function (_type, _payload, _options) {
            var args = unifyObjectStyle(_type, _payload, _options);
            var payload = args.payload;
            var options = args.options;
            var type = args.type;

            if (!options || !options.root) {
                type = namespace + type;
                if (process.env.NODE_ENV !== 'production' && !store._mutations[type]) {
                    console.error(("[vuex] unknown local mutation type: " + (args.type) + ", global type: " + type));
                    return
                }
            }

            store.commit(type, payload, options);
        }
    };

    // getters and state object must be gotten lazily
    // because they will be changed by vm update
    Object.defineProperties(local, {
        getters: {
            get: noNamespace
                ? function () {
                    return store.getters;
                }
                : function () {
                    return makeLocalGetters(store, namespace);
                }
        },
        state: {
            get: function () {
                return getNestedState(store.state, path);
            }
        }
    });

    return local
}
```

作用: 制作局部上下文的 state , getters , commit , dispatch；

`过程：`

* 用局部变量noNamespace标识namespace指定的上下文是在根模块上还是在子模块上，true对应根模块，false为子模块
* 定义一个local对象，用于代理dispatch,commit,getters,state
* 对于dispatch : 
    * 首先判断namespace对应上下文是否在根模块上
        * 如果是，则直接将 [store.dispatch](./store.prototype.dispatch.md) 赋值给dispatch
        * 如果不是，则将一个新函数赋值给dispatch,这个新函数原理如下 ： 
            * 首先通过 [unifyObjectStyle](./unifyObjectStyle.md)格式化函数参数，得到格式化后的参数对象赋值给args
            * 用局部变量 payload ，options , type存储args中对应的键的值
            * 如果options为falsely，或者options.root为falsely,即局部上下文
                * 则将type的值设置为namespace+type，即从根到当前的完整路径
                * 如果当前是在非生产环境，且在store的_actions中找不到type对应的值，则在控制台进行错误提示,并且退出
            * 最终返回store.dispatch的调用结果
* 对于commit :
    * 首先判断noNamespace的状态
        * 如果是，则直接将 [store.commit](./store.prototype.commit.md)赋值给commit
        * 如果不是，则将一个新函数赋值给commit,这个新函数原理如下 ： 
            * 首先通过 [unifyObjectStyle](./unifyObjectStyle.md)格式化函数参数，得到格式化后的参数对象赋值给args
            * 用局部变量 payload , options , type存储args中对应的键的值
            * 如果options为falsely，或者options.root为falsely,即局部上下文
                * 则将type的值设置为namespace+type，即从根到当前的完整路径
                * 如果当前是在非生产环境，且在store的_mutations中找不到type对应的值，则在控制台进行错误提示,并且退出
                * 最终返回store.commit的调用结果
* 接下来,为local对象的getters设置获取器:
    * 判断noNamespace状态，如果在根状态，则获取器为返回store.getters
    * 如果在子模块状态，则返回 [makeLocalGetters](./makeLocalGetters.md)的调用结果
* 接下来，为local对象设置state属性的获取器：
    * 该获取器返回 [getNestedState](./getNestedState.md)的调用结果


