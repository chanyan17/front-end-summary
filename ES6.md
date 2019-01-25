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

### 扩展运算符 ：将一个数组分割，并将各个项作为分离的参数传给函数\
* 与其他参数混用
```javascript
{
    let value = [-25, -50, -75, -100]
    console.log(Math.max(...value, -150))
}
```
* 拆解字符串与数组
```javascript
{
    let array = [1, 2, 3]
    console.log(...array) // 1 2 3

    let string = 'string'
    console.log(...string) // s t r i n g
}
```
* 数组拼接
```javascript
{
    let a = [1, 2, 3]
    let b = ['a', 'b', 'c']
    console.log([...a, ...b, true]) // [1, 2, 3, 'a', 'b', 'c']
}
```
### 解构赋值 ： 更方便地访问数据
```javascript
// ES5中，从对象或数组中获取信息、并将特定数据存入本地变量，书写很多重复相似的代码
{
    let a = {
        b: 1,
        c: 2
    }
    let b1 = a.b
    let c1 = a.c

    const {b, c} = a
    console.log(b, c, b1, c1) // 1 2 1 2
}
```
* 解构参数来处理可选参数
```javascript
{
    function setFn(a, b, {c, d} = {}) {
        console.log('xxx')
    }

    setFn(1, 2)
}
```
* 数组的使用
```javascript
// 结合展开运算符
const names = ['a', 'b', 'c']
const [d, ...es] = names
console.log(d) // a
console.log(es) // ['b', 'c']

// 用{}解构返回数组个数
const {length} = names
console.log(length)

// 默认值
let colors = ['red']
let [a, b = 'green'] = colors
console.log(a)
console.log(b)
```

* 数组克隆
```javascript
// ES5中使用concat()
var a = [1, 2]
var b = a.concat()

// 搭配rest参数
let c = [1, 2]
let [...d] = c
console.log(d)
```

* 数组转对象
```javascript
{
    const points = [
        [4,5],
        [10,1],
        [0,40]
    ];

    let _points = points.map(item => {
        let [x, y] = item
        return {x, y}
    })
    // 简写
    let _points = points.map(([x, y]) => {
        return {x, y}
    })
    console.log(_points)
    // [
    //   {x:4,y:5},
    //   {x:10,y:1},
    //   {x:0,y:40}
    // ]

    const points2 = [{id: 'a', name: 'a'}, {id: 'b', name: 'b'}]

    let _points2 = {}
    points2.map(item => {
        let {id, name} = item
        _points2[id] = name
    })
    // 简写
    points2.map(({id, name}) => {
        _points2[id] = name
    })
    console.log(_points2)
    // {
    //   a: 'a',
    //   b: 'b'
    // }
}
```
* 混合解构
```javascript
const data = {
    a: {b: {d: 1}},
    c: [
        {e: 2},
        {f: 3}
    ]
}
let {a, c} = data
let [item] = c
console.log(a) // {b: {d: 1}}
console.log(item) // {e: 2} 相当于提取data.c[0]
```
### 字符串模板
* 用反引号（`）标识，可以当作普通字符串使用，也可以用来定义多行字符串
* 将变量名写在${}之中，在字符串中嵌入变量和函数
```javascript
{
    const data = 'd'
    const str1 = `
        <ul>
            <li>${data}</li>
        </ul>
    `
}
```
### Class类
#### 定义类
* ES5 使用 new 关键字通过函数（也叫构造器）构造对象当做“类”来使用
```javascript
{
    // 传统构造函数
    function Animal(kind) {
        this.kind = kind
    }

    Animal.prototype.getKind = function() {
        return this.kind
    }

    let pet = new Animal('dog')
    console.log(pet.getKind()) // dog
}
```
* 通过class关键字，可以定义类（基于原型的面向对象模式的语法糖, 本质跟es5传统写法一样）
```javascript
class person {
    constructor (name, age) {
        this.name = name
        this.age = age
    }

    desc () {
        return `${this.name} is ${this.age} years old`
    }
}

let _person = new person('Xiaoming', 20)
console.log(_person.desc())
// Xiaoming is 20 years old
```
### 继承类
* ES5 通过修改原型链实现继承
```javascript
function Dog() {
    this.bark = function() {
        console.log(this.kind)
    }
}

// 绑定原型 实现继承
Dog.prototype = new Animal('smallDog')
let smallDog = new Dog()

smallDog.bark() // smallDog
```
* 通过extends关键字实现继承
```javascript
class Man extends Person {
    constructor(name, age) {
        // 有extend就必须要有super，
        // 它代表父类的构造函数，
        // 即Animal中的constructor
        super(name, age)
        this.name = name
        this.age = age
    }

    sayMan () {
        console.log(`${this.name} is ${this.age} years old`)
    }
}

const someone = new Man('David', 30)
someone.sayMan()
// David is 30 years old
```

### Promise
#### 原理
> promise 对象初始化状态为 pending ；当调用resolve(成功)，会由pending => fulfilled ；当调用reject(失败)，会由pending => rejected

#### Promise的使用流程
1. new Promise一个实例，而且要 return
2. new Promise 时要传入函数，函数有resolve reject 两个参数
3. 成功时执行 resolve，失败时执行reject
4. then 监听结果

```JavaScript
// es6对promise的使用 解决了回调地狱的问题
{
    function loadImg(src) {
        const promise = new Promise((resolve, reject) => {
            // 2. new Promise 时要传入函数，函数有resolve reject 两个参数
            var img = document.createElement('img')
            // 3. 成功时执行 resolve，失败时执行reject
            img.onload = () => {
                resolve(img)
            }
            img.onerror = () => {
                reject('error create')
            }
            img.src = src
        })
        // 1. new Promise一个实例，而且要 return
        return promise
    }

    // 成功
    // var result = loadImg('https://fakeimg.pl/300x200/333/EEE/')
    // 失败
    var result = loadImg('https://fakeimg.pl/300x200/333/EE')
    result.then(img => {
        console.log(img.width) // 成功执行：300
    }, errorMsg => {
        console.log(errorMsg) // 失败执行：error create
    })

    result.then(img => {
        console.log(img.height) // 成功执行：200
    }).catch(errorMsg => {
        console.log(errorMsg) // 失败执行：error create
    })
}
```


### Set
> 类似于数组，但是成员的值都是唯一的，不重复(去重)

#### 定义
```javascript
{
    let set = new Set([1, 2, 3, 4])
    console.log(set) // Set(4) {1, 2, 3, 4}
    // 转成数组
    let array= [...set]
    console.log(array) // [1, 2, 3, 4]
}
```

#### 使用方法
```javascript
{
    for (let i of set) {
        console.log(i)
    }

    let set2 = new Set(); // 少了分号会报错
    [1, 2, 3, 4].map(item => set2.add(item))
    console.log(set2) // Set(4) {1, 2, 3, 4}

    let bol = set2.delete(2)
    console.log(set2) // Set(3) {1, 3, 4}
    console.log(bol) // true

    let bol2 = set2.has(2)
    console.log(bol2) // false

    set2.clear()
    console.log(set2) // Set(0) {}
}
```