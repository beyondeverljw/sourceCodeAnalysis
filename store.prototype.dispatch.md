# Store.prototype.dispatch
`源码 : 427~453行`
## dispatch(_type, _payload)
```js
Store.prototype.dispatch = function dispatch(_type, _payload) {
    var this$1 = this;

    // check object-style dispatch
    var ref = unifyObjectStyle(_type, _payload);
    var type = ref.type;
    var payload = ref.payload;

    var action = {type: type, payload: payload};
    var entry = this._actions[type];
    if (!entry) {
        if (process.env.NODE_ENV !== 'production') {
            console.error(("[vuex] unknown action type: " + type));
        }
        return
    }

    this._actionSubscribers.forEach(function (sub) {
        return sub(action, this$1.state);
    });

    return entry.length > 1
        ? Promise.all(entry.map(function (handler) {
            return handler(payload);
        }))
        : entry[0](payload)
};
```

作用：派发action

`过程：`

* 将this存储在局部变量this$1中
* 通过 [unifyObjectStyle](./unifyObjectStyle.md)格式化参数，将结果存储在ref中
* 用局部变量entry存储store._actions中键为type的action
* 如果entry为falsely，直接退出。如果当前是在非生产环境下，则进行相关信息提示
* 遍历_actionSubscribers，进行消息派发
* 如果entry.length大于1，则返回一个promise,这个promise要等待entry.map返回的promise数组中的每个promise完成后被解决
* 否则，直接返回entry[0]调用后返回的promise


