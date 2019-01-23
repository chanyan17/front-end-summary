## ES6核心特性 [_原文](https://juejin.im/post/5b037b536fb9a07aa9260b39)

### let & const
>ES5 只有全局作用域和函数作用域
###### 存在的问题
* 内层变量覆盖外层变量
* 变量泄露，成为全局变量(for循环中的i)

>ES6 提供 let 和 const 来代替 var 声明变量，新的声明方式支持用大括号表示的块级作用域

###### 解决的问题
* `{}`或者`使用 const 或者 let 代替 var `构造一个立即执行的函数表达式去保证我们不污染全局作用域（不再需要立即执行的函数表达式）
* 解决在 ES5 中，如果循环体内有产生一个闭包，访问闭包外的变量产生的问题
    ![avatar](/imgs/es5&es6.png)
* ES6 不允许在同一个作用域内用 let 或 const 重复声明同名变量,解决库撞名

### 数组的扩展
#### Array.from() : 将伪数组(类数组)对象或可遍历对象转换为真数组
* 伪数组对象：一个对象的所有键名都是正整数或零，并且有length属性
* 伪数组没有数组的一般方法，直接forEach遍历会报错
###### arguments对象
    ![avatar](/imgs/PseudoArray1.png)
###### DOM 元素集
    ![avatar](/imgs/PseudoArray2.png)
##### 伪数组转真数组

```javascript
function fn() {
    let array = []
    /* 方法一 */
    // for (let i = 0; i < arguments.length; i++) {
    //     array.push(arguments[i])
    // }

    /* 方法二 */
    // array = Array.prototype.slice.call(arguments)

    /* 方法三 */
    array = Array.from(arguments)
    return array
}
console.log(fn('aa', 0, true)) // ["aa", 0, true]
```
#### Array.of() : 将一系列值转换成数组
###### 存在的问题
* Array()方法没有参数、一个参数、三个参数返回的结果不一致
* 参数个数大于1时，Array()会返回由参数组成的新数组
* 参数只有一个时，Array()会返回作为新数组的长度返回
* 无法通过Array定义只有一个元素的数组 (e.g. [3])
```javascript
let a = new Array() // []
let b = new Array(3) // [undefined, undefined, undefined]
let c = new Array(1, 2, 3) // [1, 2, 3]
```
###### 解决的问题
* Array.of()方法基本上可以用来替代Array()或new Array()，并且不存在由于参数不同而导致的重载
* 返回值一致为新数组
```javascript
{
    let a = Array.of() // []
    let b = Array.of(undefined) // [undefined]
    let c = Array.of(1, 2) // [1, 2]
}
```
#### ArrayObject.find() & ArrayObject.findIndex()
> 解决数组中是否存在某值，或者数组中匹配某值的需求，无需用for循环处理

###### find()
* 找出==第一个==符合条件的数组成员
* 参数为一个回调函数所有数组成员依次执行该回调函数，直到找出第一个返回值为true的成员，然后返回该成员
* 如果没有符合条件的成员，则返回undefined

###### findIndex()
* 返回==第一个==符合条件的数组成员的位置
* 所有成员都不符合条件，则返回-1
```javascript
{
    let a = [1, 2, 3, 4, 5, 6]
    let aa = [{id: 1}, {id: 2}, {id: 3}, {id: 4}]
    let b = a.find((n) => {
        // 偶数
        return n%2 === 0
    })
    let c = a.findIndex((n) => {
        // 5的倍数
        return n%5 === 0
    })
    let d = aa.find(n => {
        // 奇数
        return n.id % 2 !== 0
    })
    console.log(b, c) // 2 4
    console.log(d) // {id: 1}
}
```
#### ArrayObject.includes() : 某个数组是否包含给定的值，返回布尔值
###### 存在的问题
* indexOf检查是否包含某值的判断不够语义化`aString.indexOf('a') > -1`
* indexOf内部使用严格相等运算符(===),导致`NaN`的判断失误
```javascript
    NaN === NaN // false
    NaN == NaN // false
    [NaN].indexOf(NaN) // -1
```
###### 解决的问题
* ArrayObject.includes第二个参数表示搜索的起始位置，默认为0
* 如果第二个参数为负数，则表示倒数的位置
* 如果这时它大于数组长度（比如第二个参数为-4，但数组长度为3），则会重置为从0开始。
```javascript
    [NaN].includes(NaN) // true
    [1,2,3].includes(1) // true
    [1,2,3].includes(3, -1) // true
    [1,2,3,1].includes(1, 2) // true
```
#### ArrayObject.entries()、ArrayObject.keys()、ArrayObject.values() : 返回遍历器对象`Array Iterator`，配置用for...of进行遍历
* ArrayObject.entries() : 对键值对的遍历
* ArrayObject.keys() : 对键名的遍历
* ArrayObject.values() : 对键值的遍历
```javascript
{
    let a = ['a', 'b', 'c']
    for (let [index, value] of a.entries()) {
        console.log(index, value) // 0 'a' 1 'b' 2 'c'
    }
    for (let index of a.keys()) {
        console.log(index) // 0 1 2
    }
    for (let value of a.values()) {
        console.log(value) // 'a' 'b' 'c'
    }
}
```

### 箭头函数
* 缩减代码
```javascript
// ES5写法
const double1 = function(number) {
   return number * 2;
}

// ES6写法
const double2 = (number) => {
 return number * 2
}

// 可以进一步简化
const double4 = number => number * 2

// 返回对象需要加上括号
let getTempItem = id => ({ id: id, name: "Temp" })

// 简化回调函数
// 正常函数写法
[1,2,3].map(function (x) {
  return x * x;
})
// 箭头函数写法
[1,2,3].map(x => x * x); // [1, 4, 9]
```
* 改变this指向
###### 存在问题：函数里嵌套函数，导致内部this的指向发生错乱
```javascript
const team = {
  members:['Henry', 'Elyse'],
  teamName:"es6",
  teamSummary:function(){
    console.log(this.teamName) // es6
    return this.members.map(function(member){
      // this不知道该指向谁了
      console.log(this.teamName) // undefined
      return `${member}隶属于${this.teamName}小组`;
    })
  }
}
// ["Henry隶属于undefined小组", "Elyse隶属于undefined小组"]
console.log(team.teamSummary())
```
###### 解决问题
```javascript
// 方法一 let self = this
{
    const team = {
        members:['Henry', 'Elyse'],
        teamName:"es6",
        teamSummary: function() {
            let self = this
            return this.members.map(function(member){
                return `${member}隶属于${self.teamName}小组`
            })
        }
    }
    // ["Henry隶属于es6小组", "Elyse隶属于es6小组"]
    console.log(team.teamSummary())
}
```
```javascript
{
    const team = {
        members:['Henry', 'Elyse'],
        teamName:"es6",
        teamSummary: function() {
            return this.members.map(function(member){
                return `${member}隶属于${this.teamName}小组`
            }.bind(this))
        }
    }
    // ["Henry隶属于es6小组", "Elyse隶属于es6小组"]
    console.log(team.teamSummary())
}
```
```javascript
{
    const team = {
        members:['Henry', 'Elyse'],
        teamName:"es6",
        teamSummary: function() {
            return this.members.map((member) => {
                // this指向team
                return `${member}隶属于${this.teamName}小组`
            })
        }
    }
    // ["Henry隶属于es6小组", "Elyse隶属于es6小组"]
    console.log(team.teamSummary())
}
```
###### 使用注意
* 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象
```javascript
// 定义的时候所在对象为global对象，没有x属性，故this指向global对象
{
let a = {
    x: 'haha',
    f: () => {
        console.log(this.x)
    },
    z: function() {
        console.log(this.x)
    }
}
let b = {
    x: 'xixi'
}
let c = {
    x: 'emmm',
    get: function () {
        return {
            f: () => {
                console.log(this.x)
            }
        }
    }
}
function d() {
    return {
        f: () => {
            console.log(this.x)
        }
    }
}
a.f() // undefined
a.f.call(b) // undefined
a.z() // haha
a.z.call(b) // xixi
c.get().f() // emmm
c.get().f.call(b) // emmm
d.call(b).f() // xixi
}
```
* 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误
```javascript
let foo = (a) => {return a+1}
foo(1) // 2
let b = new foo(1) // 报错
```
* 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替
```javascript
{
    function a() {
        return {
            b: () => {
                console.log(arguments)
            },
            c: (...number) => {
                console.log(number)
            }
        }
    }
    a().b(1) // 类数组 length:0
    a().c(1, 23, 4) // [1, 23, 4]
}
```
* 不可以使用yield命令，因此箭头函数不能用作 Generator 函数
```javascript
// Generator 函数 function和函数名之间加一个*号
// Generator函数的一个重要特点就是需要执行next()方法才能运行
// 遇到yield命令时，表示“暂停”，并且返回yield [expression]的状态
// done: false表示遍历没有结束，还可以继续执行next()方法。
// 第二次再执行f.next()的时候，遇到了return，但后面没有表达式，所以返回值是undefined
// 一旦遇到return就表示遍历可以结束了，所以done为true
{
    let a = 0
    function *foo() {
        a += 1
        yield 1+2
        return 
    }
    let f = foo()
    console.log(a) // 0
    let b = f.next()
    console.log(a) // 1
    console.log(b) // {value: 3, done: false}
    let c = f.next()
    console.log(c) // {value: undefined, done: true}
}
```

### rest参数
* 每个函数最多只能声明一个rest参数，而且 rest参数必须是最后一个参数，否则报错
```javascript
const c = (...numbers, b) => numbers // 报错
```
* rest参数不能用于对象字面量setter之中
```javascript
let object = {
    set name(...value){   //报错
        //执行一些逻辑
    }
}
```
* 普通使用
```javascript
function foo (...number) {
    console.log(number)
}
foo(1, 2, 3, 4) // [1, 2, 3, 4]
```
* 与解构赋值组合使用
```javascript
function foo (a, b, ...number) {
    console.log(a) // 1
    console.log(b) // 2
    console.log(number) // [3, 4]

}
foo(1, 2, 3, 4)
```
* 与箭头函数结合使用
```javascript
const c = (...numbers) => numbers
console.log(c(1, 2, 3, 4)) // [1, 2, 3, 4]
```