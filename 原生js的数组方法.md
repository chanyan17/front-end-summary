## js的数组方法

#### ArrayObject.slice(start [, end])
`定义` 从已有的数组中返回选定的元素, -1 指最后一个元素
`返回值` 新数组

###### 模拟内部实现
```javascript
// this指向调用对象，用了call之后，改变this的指向
// e.g. Array.prototype.slice.call(document.querySelectorAll('li'))
Array.prototype.slice = function (start, end) {
    let result = new Array()
    let start = start || 0
    let end = end || this.length

    for (let i = start; i < end; i++) {
        result.push(this[i])
    }

    return result
}
```