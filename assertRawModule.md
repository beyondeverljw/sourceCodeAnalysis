		
[toc]
#assertRawModule
`源码256~292`
##functionAssert
```js
var functionAssert = {
    assert: function (value) {
        return typeof value === 'function';
    },
    expected: 'function'
};
```

作用：一个断言信息配置对象
functionAssert的调用结果表示接收到的value是否为function类型

##objectAssert
```js
var objectAssert = {
    assert: function (value) {
        return typeof value === 'function' ||
            (typeof value === 'object' && typeof value.handler === 'function');
    },
    expected: 'function or object with "handler" function'
};
```

作用：一个断言配置对象
assert的调用结果表示接收到的value是否是一个函数或者一个拥有handle方法的对象

##assertTypes
```js
var assertTypes = {
    getters: functionAssert,
    mutations: functionAssert,
    actions: objectAssert
};
```

作用：配置要断言的类型
表明getters和mutations需要的类型是function类型
actions需要的类型是一个function类型，或者是一个拥有handler方法的object类型

##assertRawModule(path, rawModule)
```js
function assertRawModule(path, rawModule) {
    Object.keys(assertTypes).forEach(function (key) {
        if (!rawModule[key]) {
            return
        }

        var assertOptions = assertTypes[key];

        forEachValue(rawModule[key], function (value, type) {
            assert(
                assertOptions.assert(value),
                makeAssertionMessage(path, key, type, value, assertOptions.expected)
            );
        });
    });
}
```

作用：检查rawModule中的getters,mutations,actions的值的类型是否满足期望类型，如不满足，则构建提示信息，并抛出异常。


`过程：`

* 获取assertTypes的自身可枚举非Symbol类型的键组成的数组，即['getters','mutations','actions']
* 遍历这个数组，在每次迭代中：
	* 首先判断rawModule[key]是否为truly,如果是则继续，否则直接退出本次迭代
   * assertOptions存储本次迭代中要操作的目标属性(getters , mutations , actions之一),以getters为例：
   * 利用[forEachValue](./forEachValue.md)方法遍历rawModule中的getters对象，得到每个getter
      * 利用[assert](./assert.md)断言每个getter的值是否为期待类型
		* 如果不是期待类型，则调用[makeAssertionMessage](./makeAssertionMessage.md)构建提示信息,并在assert中抛出异常
	













