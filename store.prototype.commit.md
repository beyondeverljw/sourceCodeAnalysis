# Store.prototype.commit
`源码 : 390~425行`
## commit(_type, _payload, _options)
```js
Store.prototype.commit = function commit(_type, _payload, _options) {
    var this$1 = this;

    // check object-style commit
    var ref = unifyObjectStyle(_type, _payload, _options);
    var type = ref.type;
    var payload = ref.payload;
    var options = ref.options;

    var mutation = {type: type, payload: payload};
    var entry = this._mutations[type];
    if (!entry) {
        if (process.env.NODE_ENV !== 'production') {
            console.error(("[vuex] unknown mutation type: " + type));
        }
        return
    }
    this._withCommit(function () {
        entry.forEach(function commitIterator(handler) {
            handler(payload);
        });
    });
    this._subscribers.forEach(function (sub) {
        return sub(mutation, this$1.state);
    });

    if (
        process.env.NODE_ENV !== 'production' &&
        options && options.silent
    ) {
        console.warn(
            "[vuex] mutation type: " + type + ". Silent option has been removed. " +
            'Use the filter functionality in the vue-devtools'
        );
    }
};
```

作用：提交指定类型的mutation

`过程：`

* 用this$1缓存this
* 通过[unifyObjectStyle](.unifyObjectStyle.md)对参数进行加工，最终返回一个拥有'type','payload','options'键的对象
* 用局部变量 type , payload , options存储这三个键
* 以 type , payload 设置一个mutation的配置项
* 用局部变量entry存储当前实例的_mutations中存储的type类型的函数句柄所组成的数组
* 如果entry不存在，则直接退出，如果当前环境是非生产状态，则在控制台进行错误提示
* [this._withCommit](./store.prototype._withCommit.md)
* 遍历entry,以payload作为参数依次执行entry中保存的函数
* 遍历_subscribers,以mutation配置项，和当前实例的state作为参数，依次执行_subscribers中保存的函数，即发布消息给订阅者
* 如果本次commit提供了options，并且其中包含silent键，且当前环境为非生产环境，则进行silent已经被移除的提示

