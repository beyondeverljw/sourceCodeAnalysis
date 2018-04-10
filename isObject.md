# isObject
`源码93-95行`
##isObject( Obj )
```js
function isObject(obj) {
    return obj !== null && typeof obj === 'object'
}
```

作用：判断obj是否为一个对象

`过程：`

* 首先判断obj的类型是否等于null,因为 typeof null 也等于'object'
* 然后再判断obj的类型是否等于'object'

`相关知识点：`

* Object.prototype.toString.call( obj ).slice(8,-1).toLowerCase()==='object'

