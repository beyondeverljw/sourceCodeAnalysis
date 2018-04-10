# genericSubscribe
`源码 : 533~543行`
# genericSubscribe(fn, subs)
```js
function genericSubscribe(fn, subs) {
    if (subs.indexOf(fn) < 0) {
        subs.push(fn);
    }
    return function () {
        var i = subs.indexOf(fn);
        if (i > -1) {
            subs.splice(i, 1);
        }
    }
}
```

作用：将新的订阅者(fn)添加到subs末尾,并返回一个可以让subs删除fn的函数句柄

`过程：`

* 首先判断subs中是否已有当前fn，如果没有则在subs中添加fn
* 返回一个从subs中删除自身的函数句柄


