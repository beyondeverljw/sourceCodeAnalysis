# store.prototype.replaceState
`源码 : 474~480行`
## replaceState(state)
```js
Store.prototype.replaceState = function replaceState(state) {
    var this$1 = this;

    this._withCommit(function () {
        this$1._vm._data.$$state = state;
    });
};
```

作用：通过 [_withCommit](./store.prototype._withCommit.md)直接更改store._vm的$$state

