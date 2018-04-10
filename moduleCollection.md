# ModuleCollection类
`源码170~225`

[toc]
## ModuleCollection构造器
```js
var ModuleCollection = function ModuleCollection(rawRootModule) {
    // register root module (Vuex.Store options)
    this.register([], rawRootModule, false);
};
```

作用：构造器

`过程：`

* 调用原型上的register注册模块

## <span id="get">ModuleCollection.prototype.get</span>
```js
ModuleCollection.prototype.get = function get(path) {
    return path.reduce(function (module, key) {
        return module.getChild(key)
    }, this.root)
};
```

作用：过去path数组中最后一项指向的模块，从this.root开始依次寻找

`过程：`

* 比如在配置时，指定了路径'shop/books/js/vue',经过其他方法转换成数组后：
* ['shop','books','js','vue']
* 那么调用get后，经过reduce:
    * 首先从根模块获取shop
    * 从shop模块获取其子模块books
    * ...依次类推
    * 最终返回js模块的子模块vue

## ModuleCollection.prototype.getNamespace
```js
ModuleCollection.prototype.getNamespace = function getNamespace(path) {
    var module = this.root;
    return path.reduce(function (namespace, key) {
        module = module.getChild(key);
        return namespace + (module.namespaced ? key + '/' : '')
    }, '')
};
```

作用：根据表示模块路径层级的数组path，返回对应的字符串表示形式

`过程：`

* 假设参数path为['shop','books','js']
* module作为每次获取子模块的起点，一开始为根模块
* 首先从根模块获取shop对应的模块，若该模块的namespaced为truly，则返回为'shop/',否则为''，作为下一次迭代的namespace
* 若'shop','books','js'对应的模块的namespaced均为truly，则最终返回'shop/books/js/'
* 若'shop','books','js'对应的模块的namespaced为truly,falsely,truly,则最终为'shop/js'

## ModuleCollection.prototype.update
```js
ModuleCollection.prototype.update = function update$1(rawRootModule) {
    update([], this.root, rawRootModule);
};
```

作用：用[update](./update.md)根据配置项rawRootModule从根节点this.root深度更新模块

## ModuleCollection.prototype.register
```js
ModuleCollection.prototype.register = function register(path, rawModule, runtime) {
    var this$1 = this;
    if (runtime === void 0) runtime = true;

    if (process.env.NODE_ENV !== 'production') {
        assertRawModule(path, rawModule);
    }

    var newModule = new Module(rawModule, runtime);
    if (path.length === 0) {
        this.root = newModule;
    } else {
        var parent = this.get(path.slice(0, -1));
        parent.addChild(path[path.length - 1], newModule);
    }

    // register nested modules
    if (rawModule.modules) {
        forEachValue(rawModule.modules, function (rawChildModule, key) {
            this$1.register(path.concat(key), rawChildModule, runtime);
        });
    }
};
```

作用：根据path指向的路径层级，注册模块

`过程：`

* 将this存储在局部变量this$1上
* 如果未指定runtime(即undefined)，则将其设置为true
* 如果当前环境为非生产环境，则开启调试信息，通过调用 [assertRawModule](./assertRawModule.md)来检查rawModule的'getters','mutations','actions'的类型是否为期望类型
* 根据rawModule和runtime来新建一个[module](./module.md)实例
* 如果path数组的长度为0：
    * 则将新建的module，作为根模块
    * 否则，创建一个path的副本，这个副本只包括path数组从开始到倒数第二项，作为父模块的路径
* 如果path数组长度不为0： 
    * 将得到的副本作为this.get的参数，调用[get](#get)获得当前要注册模块的最近一级父模块的引用
    * 在得到的引用上调用 [addChild](./module.md) 添加 newModule，path的最后一项是当前要注册模块的名字
* 如果rawModule存在子模块的配置，则通过 <a href="./forEachValue.html">forEachValue</a>遍历
* 在每次遍历中通过register递归注册子模块

## ModuleCollection.prototype.unregister

```js
ModuleCollection.prototype.unregister = function unregister(path) {
    var parent = this.get(path.slice(0, -1));
    var key = path[path.length - 1];
    if (!parent.getChild(key).runtime) {
        return
    }

    parent.removeChild(key);
};
```

作用：根据path指向的路径,注销模块

`过程：`

* 首先获取要注销模块的最近一级父模块(parent)
* 获取要注销模块的名字(key)
* 如果key对应的模块的runtime值为falsely,则直接退出
* 否则调用[module类](./module.md)的removeChild移除key对应的模块

