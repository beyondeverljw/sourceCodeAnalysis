# Store.prototype.subscribeAction
`源码 : 459~461行`
## Store.prototype.subscribeAction
```js
Store.prototype.subscribeAction = function subscribeAction(fn) {
    return genericSubscribe(fn, this._actionSubscribers)
};
```

作用：将新的订阅者`fn`添加到当前实例的_actionsSubscribers中。_actionsSubscribers存放的是一组action的订阅者，每次 [dispatch](./store.prototype.dispatch.md)时候为其发布消息

