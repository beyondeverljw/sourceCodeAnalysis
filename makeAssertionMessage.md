# makeAssertionMessage
`源码 294~301行`
##makeAssertionMessage(path, key, type, value, expected) 
```js
function makeAssertionMessage(path, key, type, value, expected) {
    var buf = key + " should be " + expected + " but \"" + key + "." + type + "\"";
    if (path.length > 0) {
        buf += " in module \"" + (path.join('.')) + "\"";
    }
    buf += " is " + (JSON.stringify(value)) + ".";
    return buf
}
```
作用：构建调试信息

`过程：`

* 以makeAssertionMessage(['shop','books'],'getters','count',{ key : 'hehe'} , 'function')为例
* 返回结果为：'getters should be function but "getters.count" in module "shop.books" is {"key":"hehe"}.'

