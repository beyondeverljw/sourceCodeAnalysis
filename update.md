# update
`源码 227~254行`
## update(path, targetModule, newModule)
```js
function update(path, targetModule, newModule) {
    if (process.env.NODE_ENV !== 'production') {
        assertRawModule(path, newModule);
    }

    // update target module
    targetModule.update(newModule);

    // update nested modules
    if (newModule.modules) {
        for (var key in newModule.modules) {
            if (!targetModule.getChild(key)) {
                if (process.env.NODE_ENV !== 'production') {
                    console.warn(
                        "[vuex] trying to add a new module '" + key + "' on hot reloading, " +
                        'manual reload is needed'
                    );
                }
                return
            }
            update(
                path.concat(key),
                targetModule.getChild(key),
                newModule.modules[key]
            );
        }
    }
}
```

作用 ： 根据指定的newModule参数，更新targetModule
在模块集合上调用更新的实质是通知子模块各自调用自己的更新方法(组合模式)

`过程：`

* 判断当前环境是否为生产环境，如果不是，则调用 [assertRawModule](./assertRawModule.md)进行检查newModule相关配置
* 将newModule作为参数传给要更新的对象`targetModule`的 [update](./module.md)方法，进行模块的更新
* 如果newModule存在嵌套的子模块配置 newModule.modules : 
* 则遍历其自身和原型链上的可枚举键(即每个要配置的子模块的名称)
* 在每次遍历中判断目标对象是否已经有与要更新的子模块相同名字的子模块，如果没有，则不进行对目标对象的配置
* (在非生产环境下会有相应的提示信息)
* 如果有与要更新的子模块相同名字的模块，则递归调用update

