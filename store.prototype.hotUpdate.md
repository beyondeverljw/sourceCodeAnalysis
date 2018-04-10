# Store.prototype.hotUpdate
`源码 : 519~522`
## hotUpdate(newOptions)
```js
Store.prototype.hotUpdate = function hotUpdate(newOptions) {
    this._modules.update(newOptions);
    resetStore(this, true);
};
```

作用 ： 更新_modules，重置store

[this._modules.update](./moduleCollection.md)
[resetStore](./resetStore.md)


