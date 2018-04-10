# Module类
`源码107~168`

[toc]
## Module构造器
```js
var Module = function Module(rawModule, runtime) {
    this.runtime = runtime;
    this._children = Object.create(null);
    this._rawModule = rawModule;
    var rawState = rawModule.state;
    this.state = (typeof rawState === 'function' ? rawState() : rawState) || {};
};
```

作用：Module类的构造器

`过程：`

* 将参数runtime赋值给当前实例的runtime属性
* 将当前实例的_children设置为一个没有原型链的干净对象
* 将参数rawModule赋值给当前实例的_rawModule属性
* 用局部变量rawState存储配置对象rawModule的state
* rawState可以是一个函数或一个对象。
    * 当它是函数时，则调用，将其返回的对象赋值给当前实例的state属性；
    * 否则直接将其赋值给rawState
    * 如果rawState为falsely，则将一个空对象赋值给当前实例的state属性

## prototypeAccessors$1
```js
var prototypeAccessors$1 = {namespaced: {configurable: true}};

    prototypeAccessors$1.namespaced.get = function () {
        return !!this._rawModule.namespaced
    };
```

* prototypeAccessors$1是另一个对象的描述对象，为另一个对象的namespaced属性描述为可配置，并为namespaced属性提供getter
* 利用!!将this._rawModule.namespaced布尔化 ，最终获取namespaced属性时会返回true或者false
* 这意味着在设置module的namespaced属性时，不一定非要设置成true或者false,只要是truly或者falsely即可

## Module.prototype.addChild
```js
Module.prototype.addChild = function addChild(key, module) {
    this._children[key] = module;
};
```

* addChild : 为当前实例添加子模块
* 将key作为_children对象的键，module作为该属性的值

## Module.prototype.removeChild
```js
Module.prototype.removeChild = function removeChild(key) {
    delete this._children[key];
};
```

* 通过指定键key删除当前_children对象中对应的模块

## Module.prototype.getChild
```js
Module.prototype.getChild = function getChild(key) {
    return this._children[key]
};
```

* 通过指定键key获取_children对象中已有的模块

## Module.prototype.update
```js
Module.prototype.update = function update(rawModule) {
    this._rawModule.namespaced = rawModule.namespaced;
    if (rawModule.actions) {
        this._rawModule.actions = rawModule.actions;
    }
    if (rawModule.mutations) {
        this._rawModule.mutations = rawModule.mutations;
    }
    if (rawModule.getters) {
        this._rawModule.getters = rawModule.getters;
    }
};
```

作用：用新的模块配置对象rawModule更新当前实例的_rawModule已缓存的模块配置对象。

`过程：`

* 将rawModule的namespaced赋值给当前缓存配置对象的_rawModule.namespaced<br>(若rawModule中未指定namespaced，则当前缓存的模块配置对象的namespaced会为undefined,即falsely)
* 若rawModule的actions为truly，则用其actions覆盖当前已经缓存的模块配置对象的actions
* 若rawModule的mutations为truly，则用其mutations覆盖当前已经缓存的模块配置对象的mutations
* 若rawModule的getters为truly，则用其getters覆盖当前已经缓存的模块配置对象的getters

##Module.prototype.forEachChild
```js
Module.prototype.forEachChild = function forEachChild(fn) {
    forEachValue(this._children, fn);
};
```

作用：遍历子模块，为每个子模块调用一次fn

`过程：`

* [forEachValue](./forEachValue.md)的作用为遍历对象自身可枚举非Symbol类型的键，并将该键对应的值和其自身作为参数传递给fn

## Module.prototype.forEachGetter
```js
Module.prototype.forEachGetter = function forEachGetter(fn) {
    if (this._rawModule.getters) {
        forEachValue(this._rawModule.getters, fn);
    }
};
``` 

作用：遍历当前缓存模块配置对象的getters，为getters中的每一个getter调用一次fn

## Module.prototype.forEachAction
```js
Module.prototype.forEachAction = function forEachAction(fn) {
    if (this._rawModule.actions) {
        forEachValue(this._rawModule.actions, fn);
    }
};
```

作用：遍历当前缓存模块配置对象的actions，为actions中的每一个action调用一次fn

##Module.prototype.forEachMutation
```js
Module.prototype.forEachMutation = function forEachMutation(fn) {
    if (this._rawModule.mutations) {
        forEachValue(this._rawModule.mutations, fn);
    }
};
```

作用：遍历当前缓存模块配置对象的mutations，为mutations中的每一个mutation调用一次fn

##Object.defineProperties(Module.prototype, prototypeAccessors$1);
作用：为Module的原型对象设置namespaced描述和getter




