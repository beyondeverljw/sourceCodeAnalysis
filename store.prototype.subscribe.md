# Store.prototype.subscribe
`源码 : 455~457行`
## Store.prototype.subscribe
```js
Store.prototype.subscribe = function subscribe(fn) {
    return genericSubscribe(fn, this._subscribers)
};
```

将fn添加到当前实例的订阅者集合(_subscriber)中,_subsriber中存放的是一组mutation订阅者，当[commit](./store.prototype.commit.md)被调用时，为其发布消息
[genericSubscribe](./genericSubscribe.md)
返回一个可以从_subscribers中删除fn的函数句柄

