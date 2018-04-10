# unifyObjectStyle
`源码 : 809~821行`
##unifyObjectStyle(type, payload, options)
```js
function unifyObjectStyle(type, payload, options) {
    if (isObject(type) && type.type) {
        options = payload;
        payload = type;
        type = type.type;
    }

    if (process.env.NODE_ENV !== 'production') {
        assert(typeof type === 'string', ("Expects string as the type, but found " + (typeof type) + "."));
    }

    return {type: type, payload: payload, options: options}
}
```

作用：根据参数返回一个指定格式的对象，这个对象包含'type' , 'payload' , 'options'键

`过程：`

* 如果type参数是一个对象，并且具有type属性，则调整参数
* 将payload赋值给options,将type对象赋值给payload
* 将type对象的type属性赋值给type变量
* 如果参数转换后，type局部变量不是string类型，则在非生产环境下通过 [assert](./assert.md)方法抛出异常
* 如果type参数是一个字符串，则直接返回格式化好的对象
* 也就是说该方法可以通过两种参数形式的调用：
    * 第一种是 unifyObjectStyle({ type: 'xxx','uName' : 'fff' } , option)
    * 第二种是 unifyObjectStyle('typexxx',payload,option)

