# install
`源码 : 823~834行`
##install(_Vue)
```js
function install(_Vue) {
    if (Vue && _Vue === Vue) {
        if (process.env.NODE_ENV !== 'production') {
            console.error(
                '[vuex] already installed. Vue.use(Vuex) should be called only once.'
            );
        }
        return
    }
    Vue = _Vue;
    applyMixin(Vue);
}
```

作用：安装vuex

`过程：`

* 如果变量Vue(源码303行声明的)已经被赋值，且其值与参数_Vue相等，证明其已经被安装过了
* 如果当前是在非生产黄精，则在控制台进行错误提示
* 若没安装过，则将参数_Vue赋值给Vue变量
* 调用[applyMixin](./applyMixin.md)进行全局混入



