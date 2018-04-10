
#assert
`源码101~105行`
##assert(condition, msg)
```js
function assert(condition, msg) {
    if (!condition) {
        throw new Error(("[vuex] " + msg))
    }
}
```

作用：接受一个判断条件，和相关信息，条件值为falsely时，根据传递进来的信息抛出异常


`过程：`

* 如果condition的值为falsely,则抛出异常，msg作为异常的信










