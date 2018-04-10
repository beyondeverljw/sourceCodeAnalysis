# normalizeMap
`原码 : 947~955行`
#normalizeMap(map)#
```js
function normalizeMap(map) {
    return Array.isArray(map)
        ? map.map(function (key) {
            return ({key: key, val: key});
        })
        : Object.keys(map).map(function (key) {
            return ({key: key, val: map[key]});
        })
}
```

作用：格式化参数map,返回一个数组，使该数组中的每一项是一个包含'key','val'键的对象

`过程：`

* 当map参数是一个数组时，通过map函数对数组的每一项加工，并将这些加工好的每一项组成的数组作为normalizeMap函数的结果返回
* 当map是一个对象时，首先通过keys获取map的自身的，可枚举的，非Symbol类型的键组成的数组
* 然后通过map函数对该数组的每一项进行加工，并将这些加工项组成的数组作为normalizeMap函数的结果返回

