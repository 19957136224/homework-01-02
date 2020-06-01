### 函数式编程
函数式编程(Functional Programming. FP), FP 是编程范式之一， 我们常听说的编程范式还有面向过程编程、 面向对象编程。
#### 面向对象编程的思维方式:
把现实世界中的事物抽象成程序世界中的类和对象，通过封装继承和多态来演示事物事件的联系
函数式编程的思维方式:把现实世界的事物和事物之间的联系抽象到程序世界(对运算过程进行抽象)

#### 程序的本质
根据输入通过某种运算获得相应的输出，程序开发过程中会涉及很多有输入和输出的函数. x→f(联系、 映射)→y，y=f(x)

函数式编程中的函数指的不是程序中的函数(方法)，而是数学中的函数即映射关系，例如: y = sin(x), x和y的关

相同的输入始终要得到相同的输出(纯函数).函数式编程用来描述数据(函数)之间的映射


### 函数是一等公民(First class Function)

函数可以存储在变量中函数作为参数

函数作为返回值

在JavaScript中函数就是一个普通的对象(可以通过new Function() ),我们可以把函数存储到变量/数组中，它还可以作为另一个函数的参数和返回值，甚至我们可以在程序运行的时候通过new Function( 'alert(1)')来构造一个新的函数。

把函数赋值给变量

赋值的不是方法的调用而是方法本身

```js

const obj = {
    index(posts) { return Views.index(posts) }
}

// 优化

const obj = {
    index: Views.index
}
```

### 高阶函数

可以把函数作为参数传递给另一个函数

可以把函数作为另一个函数的返回结果

抽象可以帮我们屏蔽细节，只需要关注与我们的目标高阶函数是用来抽象通用的问题

```js

const once = function (fn) {
    let done = false
    return function () {
        if (!done) {
            done = true
            return fn.apply(this, arguments)
        }
    }
}

const pay = once(function (money) {
    console.log(`支付 ${money} RMB`)
})

pay(5)
pay(5)
pay(5)
pay(5)
```

### 闭包(Closure)
函数和其周围的状态(词法环境)的引用捆绑在一起形成闭包。

可以在另一个作用域中调用一个函数的内部函数井访可到该函数的作用城中的成员

#### 闭包的本质
函数在执行的时候会放到一个执行栈上当函数执行完毕之后会从执行栈上移除，但是堆上的作用域成员因为被外部引用不能释放，因此内部函数依然可以访问外部函数的成员

### 纯函数

相同的输入永远会得到相同的输出，而且没有任何可观察的副作用.纯函数就类似数学中的函数(用来描述输入和输出之间的关系)。y= f(x)

好处：
1. 可缓存
    - 因为纯函数对相同的输入始终有相同的结果，所以可以把纯函数的结果缓存起来
2. 可测试
    - 纯函数让测试更方便
3. 并行处理
    - 在多线程环境下并行操作共享的内存数据很可能会出现意外情况
    - 纯函数不需要访问共享的内存数据，所以在并行环境下可以任意运行纯函数（Web Worker）

```js
// 模拟memoize

function memoize (f) {
    let cache = {}
    return function () {
        let key = JSON.stringify(arguments)
        cache[key] = cache[key] || f.apply(f, arguments)
        return cache[key]
    }
}
```

### 柯里化(Currying)

当一个函数有多个参数的时候先传递一部分参数调用它(这部分参数以后远不变)

然后返回一个新的函数接收剩余的参数，返回结果

柯里化可以让我们给一个函数传递较少的参数得到一个已经记住了某些固定参数的新函数

这是一种对函数参数的缓存

让函数变的更灵活，让函数的粒度更小

可以把多元函数转换成元函数，可以组合使用函数产生强大的功能

```js
function curry (func) {
    return function curriedFn(...args) {
        // 判断实参args和形参func的个数
        if (args.length < func.length) {
            return function () {
                return curriedFn(...args.concat(Array.from(arguments)))
            }
        }
        return func(...args)
    }
}
```

### 函数组合(compose)
如果个函数要经过多 个函故处理才能得到最终值，这个时候可以把中间过程的函数合并成一个函数

函数就像是数据的管道，函数组合就是把这些管道连接起来，让数据穿过多个管道形成最终结果

函数组合默认是从右到左执行

### Point Free
我们可以把数据处理的过程定义成与数据无关的合成运算，不需要用到代表数据的那个参数，只要把简单的运算步骤合成到一起，在使用这种模式之前我们需要定义一些辅助的基本运算函数。

- 不需要指明处理的数据
- 只需要合成运算过程
- 需要定义一些辅助的基本运算函数

### Functor (函子)

- 容器:包含值和值的变形关系(这个变形关系就是函数)
- 函子:是一个特殊的容器，通过一个普通的对象来实现，该对象具有map方法，map方法可以运行一个函数对值进行处理(变形关系)
- 
函数式编程的运算不直接操作值，而是由函子完成

函子就是一个实现了 map契约的对象

我们可以把函子想象成一 个盒子，这个盒子里封装了一个值

想要处理盒子中的值，我们需要给盒子的map方法传递一个处理值的函数 (纯函数)，由这个函数来对值进行处理

最终map方法返回一个包含新值的盒子(函子)

### MayBe函子

我们在编程的过程中可能会遇到很多错误，需要对这些错误做相应的处理

MayBe函子的作用就是可以对外部的空值情况做处理(控制副作用在允许的范围)

```js

class MayBe {
    static of (value) {
        return new MayBe(this.value)
    }
    
    constructor (value) {
        this._value = value
    }
    
    map (fn) {
        return this.isNothing() ? MayBe.of(null) : MayBe.of(fn(this._value))
    }
    
    isNothing () {
        return this._value === null || this._value === undefined
    }
}
```

### IO函子

IO函子中的_value 是一个函数，这里是把函数作为值来处理

IO函子可以把不纯的动作存储到value 中，延迟执行这个不纯的操作(情性执行)，包装当前的纯操作

把不纯的操作交给调用者来处理

```js
class IO {
    static of (value) {
        return new IO(function() {
            return value
        })
    }
    
    constructor (fn) {
        this._value = fn
    }
    
    map (fn) {
        return new IO(fp.flowRight(fn, this._value))
    }
}
```

### Task异步执行
folklale一个标准的函数式编程库
和lodash, ramda不同的是，他没有提供很多功能函数
只提供了一些函数式处理的操作，例如: compose, curry等，一些函子Task. Either. MayBe等

### Pointed函子
Pointed函子是实现了of静态方法的函子

of方法是为了避免使用new
来创建对象，更深层的含义是ol方法用来把值放到上下文 Context (把值放到容器中，使用map来处理值)

### Monad函子
Monad函子是可以变扁的Pointed函子，IO(IO(x))
一个函子如果具有join和of两个方法并遵守一些定律就是一个Monad 

```js
class IO {
    static of (value) {
        return new IO(function() {
            return value
        })
    }
    
    constructor (fn) {
        this._value = fn
    }
    
    map (fn) {
        return new IO(fp.flowRight(fn, this._value))
    }
    
    join () {
        return this._value()
    }
    
    flatMap (fn) {
        return this.map(fn).join()
    }
}
```