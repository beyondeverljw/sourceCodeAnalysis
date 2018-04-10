

# 读Vuex源码--基于3.0.1 esm版

Vue技术栈作为当今前端的核心技能之一，应该熟练掌握，不仅应该知其然，更应该知其所以然。以下部分为个人读vuex源码心得，主要分为三个部分，一个部分是从整体看其原理，一个是局部每个函数的实现过程分析，还有一部分是补充[文档](https://vuex.vuejs.org/zh-cn/core-concepts.html)中未提及的部分

[toc]

## 源代码
[vuex.esm.js v3.0.1](./source-code.md)

## 从整体看
整个存储体系是由module节点组成的一棵树，而 ModuleCollection则是这棵树的管理者 ，这是一个典型的组合模式。
当通过moduleCollection进行register,update,unRegister时，ModuleCollection是不进行具体的操作的，他只是把这些任务派发给具体的module，通过module自身的 addChild , update , removeChild进行实际的操作。此外，ModuleCollection还提供了对这棵树路径操作的办法，比如将从根节点到指定的子节点的路径以字符串的方式表达的`getNamespace(path)`；比如根据指定路径获取module节点的 `get( path )` 方法。

而每个module提供两部分功能，一类是操作自身，一类是管理子节点。操作自身的方法主要对应着module自身的_rawModule进行操作；管理子节点，主要对应着module的_children进行操作。

* 操作自身的有 ： update , forEachGetter , forEachMutation , forEachAction。
* 管理子节点的有 ： addChild , removeChild , getChild , forEachChild

当调用 `new Vuex.Store( options )`, 主要过程是：

*     通过 `this._modules = new ModuleCollection(options);`构造这棵树。
*     通过 `installModule(this, state, [], this._modules.root);` 将这颗树的相关信息进行处理并存储到我们store实例的各个属性中，当对store实例的各种操作，实质上都是在用这棵树提供的信息功能进行相应的处理。

## 局部
[applyMixin : 源码6~40行](./applyMixin.md)
[forEachValue : 源码87~91行](./forEachValue.md)
[isObject : 源码93~95行](./isObject.md)
[isPromise( val ) : 源码97~99行](./isPromise.md)
[assert : 源码101~105行](./assert.md)
[Module类 : 源码107~168](./module.md)
[ModuleCollection类 : 源码170~225行]( ./moduleCollection.md)
[update : 源码227~254行](./update.md)
[assertRawModule : 源码256~292行](./assertRawModule.md)
[makeAssertionMessage : 源码294~301行](./makeAssertionMessage.md)
[store构造函数 : 源码305~376行](./store.md)
[prototypeAccessors : 源码378~388行](./prototypeAccessors.md)
[Store.prototype.commit : 源码390~425行](./store.prototype.commit.md)
[Store.prototype.dispatch : 源码427~453行](./store.prototype.dispatch.md)
[Store.prototype.subscribe : 源码455~457行](./store.prototype.subscribe.md)
[Store.prototype.subscribeAction : 源码459~461行](./store.prototype.subscribeAction.md)
[Store.prototype.watch 源码 : 463~472行](./store.prototype.watch.md)
[Store.prototype.replaceState : 源码474~480行](./store.prototype.replaceState.md)
[Store.prototype.registerModule : 源码482~498行](./store.prototype.registerModule.md)
[Store.prototype.unregisterModule : 源码500~517行](./store.prototype.unregisterModule.md)
[Store.prototype.hotUpdate : 源码519~522](./store.prototype.hotUpdate.md)
[Store.prototype._withCommit : 源码524~531行](./store.prototype._withCommit.md)
[genericSubscribe : 源码533~543行](./genericSubscribe.md)
[resetStore : 源码545~555行](./resetStore.md)
[resetStoreVM : 源码557~607行](./resetStoreVM.md)
[installModule : 源码609~648行](./installModule.md)
[makeLocalContext : 源码650~713行](./makeLocalContext.md)
[makeLocalGetters : 源码715~740行](./makeLocalGetters.md)
[registerMutation : 源码742~747行](./registerMutation.md)
[registerAction : 源码749~772行](./registerAction.md)
[registerGetter : 源码774~789](./registerGetter.md)
[enableStrictMode : 源码791~799行](./enableStrictMode.md)
[getNestedState : 源码801~807行](./getNestedState.md)
[unifyObjectStyle : 源码809~821行](./unifyObjectStyle.md)
[install: 源码823~834行](./install.md)
[mapState : 源码836~861行](./mapState.md)
[mapMutations : 源码863~887行](./mapMutations.md)
[mapGetters : 源码889~910行](./mapGetters.md)
[mapActions : 源码912~936行](./mapActions.md)
[createNamespacedHelpers : 源码938~945行](./createNamespacedHelpers.md)
[normalizeMap : 源码947~955行](./normalizeMap.md)
[normalizeNamespace : 源码957~965行](./normalizeNamespace.md)
[getModuleByNamespace : 源码969~975行](./getModuleByNamespace.md)

##补充
###关于mapState
当其第二个参数（第一个参数是namespace，可以没有）为对象时，该对象的键对应的值是函数时，除了获取namespace指定的state，还会获取对应的getters
源码中有：

```js
    return typeof val === 'function'
            ? val.call(this, state, getters)
            : state[val]
```

因此调用时可以：

```js
    mapState({
        field : function( state , getters ){}
    })
```

### 关于mapMutations
当其第二个参数（第一个参数是namespace，可以没有）为对象时，该对象的键对应的值可以是函数，该函数第一个参数会是namespace指向的commit
源码中：

```js
    return typeof val === 'function'
            ? val.apply(this, [commit].concat(args))
            : commit.apply(this.$store, [val].concat(args))
```

因此调用时可以：

```js
    mapMutations({
        field : function( commit , payload ){
            //其他代码...
            commit('type',payload);
            //...
        }
    })
```

### 关于mapActions
当其第二个参数（第一个参数是namespace，可以没有）为对象时，该对象的键对应的值可以是函数，该函数第一个参数会是namespace指向的dispatch
源码中：

```js
    return typeof val === 'function'
            ? val.apply(this, [dispatch].concat(args))
            : dispatch.apply(this.$store, [val].concat(args))
```

因此调用时可以：

```js
    ...mapActions({
        asyncAdd( dispatch , payload){
            dispatch('type',payload)
                .then(function( data ){
                    console.log( '数据已经递交：' , data )
                })
        }
    })
```





                                                                            国安信徒
                                                                            2018-04-05



