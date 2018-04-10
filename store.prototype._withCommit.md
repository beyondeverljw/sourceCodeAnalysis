# Store.prototype._withCommit
`源码 : 524~531行`
## Store.prototype._withCommit : 源码524~531行
```js
Store.prototype._withCommit = function _withCommit(fn) {
    var committing = this._committing;
    this._committing = true;
    fn();
    this._committing = committing;
};
Object.defineProperties(Store.prototype, prototypeAccessors);
```

作用：在严格模式下，暂时开启修改store._vm.$$state的权限
在执行fn前将当前实例的_committing的设为true，执行完成后恢复原来值

`过程：`

* 用committing局部变量保存当前实例的_committing的值
* 将当前实例的_committing设置为true
* 执行fn
* 恢复当前实例的_committing的值
* 根据prototypeAccessors的描述定义 Store.prototype中相关属性

