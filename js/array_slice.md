## [].slice.call('123')/Array.prototype.slice.call('123')

这两者是===的，这种方法主要是用来将函数的 arguments 转换成数组，如：

```
function toList() {
      return [].slice.call(arguments)
}
console.log(toList(1, 2, 3, 4))

```
