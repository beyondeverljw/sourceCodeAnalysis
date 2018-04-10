# forEachValue
`源码87-91行`
##forEachValue( Obj , fn )
```js
function forEachValue(obj, fn) {
    Object.keys(obj).forEach(function (key) {
        return fn(obj[key], key);
    });
}
```

作用：为obj中自身的可枚举的属性及对应的值执行一次fn

`过程：`

* 获取obj自身的、可枚举的、非symbol类型的属性，并遍历
* 在每次遍历中：
	* 将obj[key]及key作为参数传入fn，调用
	* 返回调用的结果（ 此处不加return亦可，forEach不会返回新数组，返回值永远是undefined ）

`相关知识点：`

* Object.keys : 返回一个对象自身的，可枚举的 ，非Symbol类型的属性名数组
* Object.values : 返回一个对象自身的，可枚举的，非Symbol类型的属性对应的值组成的数组
* Object.entries : 返回一个对象自身的，可枚举的，非Symbol类型的属性及其值组成的"键值对"数组，该数组是一个二维数组，数组中的每个元素是一个键值对数组。
* Object.getOwnPropertyNames : 返回一个对象自身的除Symbol类型意外的所有属性名组成的数组（即自身的可枚举和不可枚举属性）
* Reflect.ownKeys : 返回一个对象自身的所有属性键（ 即自身的可枚举，不可枚举和Symbol类型的属性键 ）
* for...in : 遍历自身及其原型链上的可枚举的、非Symbol类型的属性
* for...of : 在可迭代对象上创建一个迭代循环，迭代规则由迭代对象自身定义
* 可迭代对象是指一个对象自身或其圆形脸上设置了Symbol.iterator






