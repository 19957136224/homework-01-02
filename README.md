### 1.描述引用计数的工作原理和优缺点
内部有引用计数器，来维护当前对象的引用数，判断当前引用数是否为0，变量指向对象空间数字加一，为0时GC就会回收

优点：
- 发现垃圾时立即回收
- 最大限度减少程序暂停

缺点：
- 无法回收循环引用的对象
- 资源消耗大

### 2.描述标记整理算法的工作流程
遍历所有对象找标记活动对象，遍历所有对象清除没有标记对象，清除阶段先执行整理，移动对象位置，再回收相应的空间

优点：
- 减少碎片化空间

缺点：
- 不会立即回收垃圾对象

### 3.描述V8中新生代存储区垃圾回收的流程
新生代内存分为两个空间，使用空间为From，空闲空间为To，活动对象存储于From空间，标记整理后将活动对象复制至To空间，再将From空间释放，然后From和To交换

### 4.描述增量标记算法在何时使用及工作原理
程序执行暂停时进行垃圾回收，交替进行

在老生代对象遍历对象时进行标记，将一整段垃圾回收操作拆分成多个小部分，组合完成整个垃圾回收。


### 代码题1

```js
const fp = require('lodash/ fp')
// 数据
// horsepower 马力，dollar_value 价格，in_stock 库存

const cars =[{
    name: " Ferrari FF",
    horsepower: 660,
    dollar_value: 700000,
    in_stock: true
}, {
    name: "Spyker C12 Zagato",
    horsepower: 650,
    dollar_value: 648000,
    in_stock: false
}, {
    name: "Jaguar XKR-S",
    horsepower: 550,
    dollar_value: 132000,
    in_stock: false
}, {
    name: "Audi R8",
    horsepower: 525,
    dollar_value: 114200,
    in_stock: false
}, {
    name: "Aston Martin One-77",
    horsepower: 750,
    dollar_value: 1850000,
    in_stock: true
}, {
    name: "Pagani Huayra",
    horsepower: 700,
    dollar_value: 1300000,
    in_stock: false
}]

```

#### 练习1 使用函数组合fp.flowRight()重新实现下面这个函数

```js
// 题目
let isLastInStock = function (cars) {
    // 获取最后一条数据
    let last_car = fp.last(cars)
    // 获取最后一条数据的 in_stock 属性值
    return fp.prop('in_stock', last_car)
}

// 答案
let isLastInStock = fp.flowRight(fp.prop('in_stock'), fp.last)

isLastInStock(cars)
```

#### 练习2 使用fp.flowRight()、fp.prop()和fp.first()获取第一个car的name

```js
let firstCarName = fp.flowRight(fp.prop('name'), fp.first)

firstCarName(cars)
```

#### 练习3 使用帮助函数_average重构averageDollarValue，使用函数组合的方式实现

```js
// 题目
let _average = function (xs) { return fp.reduce(fp.add, 0, xs) / xs.length}

let averageDollarValue = function (cars) {
    let dollar_values = fp.map(function (car) {
        return car.dollar_value
    }, cars)
    return _average(dollar_values)
}

// 答案
let averageDollarValue = fp.flowRight(_average, fp.map(fp.prop('dollar_value')))

averageDollarValue(carss)
```

#### 练习4 使用flowRight写一个sanitizeNames()函数，返回一个下划线连接的小写字符串，把数组中的name转换为这种形式:例如: sanitizeNames(["Hello World"]) => ["hello_world"]

```js
let _underscore = fp.replace(/\W+/g, '_')

let sanitizeNames = fp.flowRight(fp.map(fp.flowRight(_underscore, fp.prop('name'))))

sanitizeNames(cars)
```

### 代码题2

```js
// support.js
class Container {
    static of (value) {
        return new Container(value)
    }

    constructor (value) {
        this._value = value
    }

    map (fn) {
        return Container.of(fn(this._value))
    }
}

class Maybe {
    static of (x) {
        return new MayBe(x)
    }

    isNothing () {
        return this._value === null || this._value === undefined
    }
    
    constructor (x) {
        this._value = x
    }
    
    map (fn) {
        return this.isNothing() ? this : MayBe.of(fn(this._value))
    }
}

module.exports = {
    Maybe,
    Container
}
```

#### 练习1: 使用fp.add(x, y)和fp.map(f, x)创建一个能让functor里的值增加的函数ex1

```js
const fp = require('lodash/fp')
const { Maybe, Container } = require('./support')
let maybe = Maybe.of([5, 6, 1])

let ex1 = maybe.map(fp.map(fp.add(1)))._value
```

#### 练习2: 实现一个函数ex2，能够使用fp.first 获取列表的第一个元素

```js
const fp = require('lodash/fp')
const { Maybe, Container } = require('./support')
let xs = Container.of(['do', 'ray', 'me', 'fa', 'so', 'la', 'ti', 'do'])

let ex2 = xs.map(fp.first)._value
```

#### 练习3: 实现一个函数ex3，使用safeProp和fp.first找到user的名字的首字母

```js
const fp = require('lodash/fp')
const { Maybe, Container } = require('./support')

let safeProp = fp.curry(function (x, o) {
    return Maybe.of(o[x])
})
let user = { id: 2, name: "Albert" }

let ex3 = safeProp("name")(user).map(fp.first)._value
```

#### 练习4: 使用Maybe重写ex4，不要有if语句

```js
const fp = require('lodash/fp')
const { Maybe, Container } = require('./support')

let ex4 = function (n) {
    if (n) { return parseInt(n) }
}

// 重写
let ex4 = function (n) {
    return Maybe.of(n).map(parseInt)
}
```