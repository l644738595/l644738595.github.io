---
layout: post
title:  "JavaScript 函数式编程术语大全"
date:   2017-08-31 12:44:23 +0800
categories: javascript
---
函数式编程(FP)有许多优点，它也越来越流行了。然而，每个编程范式都有自己独特的术语，函数式编程也不例外。通过提供的这张术语表，希望使你学习函数式编程变得容易些。

示例以 JavaScript (ES2015) 方式呈现。 [Why JavaScript?](https://github.com/hemanth/functional-programming-jargon/wiki/Why-JavaScript%3F)

在适用的情况下，本文档使用的术语定义在 [Fantasy Land spec](https://github.com/fantasyland/fantasy-land) 中

**目录**

- [参数个数 Arity](#arity)
- [高阶组件 Higher-Order Functions (HOF)](#higher-order-functions-hof)
- [偏应用函数 Partial Application](#partial-application)
- [柯里化 Currying](#currying)
- [闭包 Closure](#closure)
- [自动柯里化 Auto Currying](#auto-currying)
- [函数合成 Function Composition](#function-composition)
- [Continuation](#continuation)
- [纯函数 Purity](#purity)
- [副作用 Side effects](#side-effects)
- [幂等 Idempotent](#idempotent)
- [Point-Free Style](#point-free-style)
- [断言 Predicate](#predicate)
- [约定 Contracts](#contracts)
- [范畴 Category](#category)
- [值 Value](#value)
- [常量 Constant](#constant)
- [函子 Functor](#functor)
- [Pointed Functor](#pointed-functor)
- [提升 Lift](#lift)
- [引用透明性 Referential Transparency](#referential-transparency)
- [等式推理 Equational Reasoning](#equational-reasoning)
- [Lambda](#lambda)
- [λ演算 Lambda Calculus](#lambda-calculus)
- [惰性求值 Lazy evaluation](#lazy-evaluation)
- [Monoid](#monoid)
- [Monad](#monad)
- [Comonad](#comonad)
- [Applicative Functor](#applicative-functor)
- 态射 Morphism
  - [自同态 Endomorphism](#endomorphism)
  - [同构 Isomorphism](#isomorphism)
- [Setoid](#setoid)
- [半群 Semigroup](#semigroup)
- [Foldable](#foldable)
- [类型签名 Type Signatures](#type-signatures)
- 代数数据类型 Algebraic data type
  - [Sum type](#sum-type)
  - [Product type](#product-type)
- [Option](#option)
- [JavaScript 中的函数式编程库 Functional Programming Libraries in JavaScript](#functional-programming-libraries-in-javascript)

<div id="arity" tabindex="-1"></div>
## Arity

函数所需的参数个数。来自于单词 unary, binary, ternary 等等。这个单词是由 `-ary` 与 `-ity` 两个后缀组成。例如，一个带有两个参数的函数被称为二元函数或者它的 arity 是2。它也被那些更喜欢希腊词根而非拉丁词根的人称为 `dyadic`。同样地，带有可变数量参数的函数被称为 `variadic`，而二元函数必须给出两个且只有两个参数，见下文，柯里化(Currying) 和 偏应用函数(Partial Application) 。

```javascript
const sum = (a, b) => a + b
 
const arity = sum.length
console.log(arity) // 2
 
// The arity of sum is 2
```

<div id="higher-order-functions-hof" tabindex="-1"></div>
## 高阶函数 Higher-Order Functions (HOF)

一个函数，以函数为参数 或/和 返回一个函数。

```javascript
const filter = (predicate, xs) => xs.filter(predicate)
```

```javascript
const is = (type) => (x) => Object(x) instanceof type
```

```javascript
filter(is(Number), [0, '1', 2, null]) // [0, 2]
```

<div id="partial-application" tabindex="-1"></div>
## 偏应用函数 Partial Application

偏应用一个函数意思是通过预先填充原始函数的部分(不是全部)参数来创建一个新函数。

```javascript
// 创建偏应用函数
// 带一个函数参数 和 该函数的部分参数
const partial = (f, ...args) =>
  // 返回一个带有剩余参数的函数
  (...moreArgs) =>
    // 通过全部参数调用原始函数
    f(...args, ...moreArgs)
 
// 原始函数
const add3 = (a, b, c) => a + b + c
 
// 偏应用 `2` 和 `3` 到 `add3` 给你一个单参数的函数
const fivePlus = partial(add3, 2, 3) // (c) => 2 + 3 + c
 
fivePlus(4) // 9
```

你也可以使用 `Function.prototype.bind` 来实现偏应用函数:

```javascript
const add1More = add3.bind(null, 2, 3) // (c) => 2 + 3 + c
```

偏应用函数应用通过对复杂的函数填充一部分数据来构成一个简单的函数。[柯里化(Curried)](#currying) 通过偏应用函数实现。

<div id="currying" tabindex="-1"></div>

## Currying

将多个参数的函数(多元函数) 转换为 一元函数的过程。

每当函数被调用时，它仅仅接收一个参数并且返回带有一个参数的函数，直到所有的参数传递完成。

```javascript
const sum = (a, b) => a + b
 
const curriedSum = (a) => (b) => a + b
 
curriedSum(40)(2) // 42.
 
const add2 = curriedSum(2) // (b) => 2 + b
 
add2(10) // 12
```

<div id="closure" tabindex="-1"></div>

## 闭包 Closure

闭包是访问其作用域之外的变量的一种方法。正式一点的解释，闭包是一种用于实现词法作用域的命名绑定的技术。它是一种用环境存储函数的方法。

闭包是一个作用域，在这个作用域能够捕获访问函数的局部变量，即使执行已经从定义它的块中移出。即，它们允许在声明变量的块执行完成之后保持对作用域的引用。

```javascript
const addTo = x => y => x + y
var addToFive = addTo(5)
addToFive(3) // returns 8
```

函数 `addTo()` 返回一个函数（内部调用 `add()` ），将它存储在一个名为 `addToFive` 的变量中，并柯里化(Curried)调用，参数为 5 。

通常，当函数 `addTo` 完成执行时，其作用域与局部变量 `add`，`x`，`y` 不可访问。但是，它在调用 `addToFive()` 时返回 8。这意味着即使代码块执行完成后，函数 `addTo` 的状态也被保存，否则无法知道 `addTo` 被调用为 `addTo(5)`，`x` 的值设置为 5。

词法作用域是能够找到 `x` 和 `add` 的值的原因 – 已完成执行的父级私有变量。这就称为 Closure(闭包) 。

堆栈伴随着函数的词法作用域存储在父对象的引用形式中。这样可以防止闭包和底层变量被当做垃圾回收（因为至少有一个实时引用）。

Lambda Vs Closure(闭包) ：lambda 本质上是一个内联定义的函数，而不是声明函数的标准方法。lambda 经常可以作为对象传递。

闭合是一个函数，通过引用其函数体外部的字段来保持对外部变量的引用。

**进一步阅读 / 来源**
* [Lambda Vs Closure](http://stackoverflow.com/questions/220658/what-is-the-difference-between-a-closure-and-a-lambda)
* [How do JavaScript Closures Work?](http://stackoverflow.com/questions/111102/how-do-javascript-closures-work)

<div id="auto-currying" tabindex="-1"></div>

## 自动柯里化 Auto Currying

将一个将多个参数的函数转换为一个参数的函数，如果给定的参数数量少于正确的参数，则返回一个函数，该函数将获得其余的参数。当函数得到正确数量的参数时，它就会被求值。

lodash 和 Ramda 都有一个 `curry` 函数，使用的就是这种工作方式。

```javascript
const add = (x, y) => x + y
 
const curriedAdd = _.curry(add)
curriedAdd(1, 2) // 3
curriedAdd(1) // (y) => 1 + y
curriedAdd(1)(2) // 3
```

**进一步阅读**
* [Favoring Curry](http://fr.umio.us/favoring-curry/)
* [Hey Underscore, You’re Doing It Wrong!](https://www.youtube.com/watch?v=m3svKOdZijA)

<div id="function-composition" tabindex="-1"></div>

## 函数合成 Function Composition

将两个函数合成在一起构成第三个函数，其中一个函数的输出是另一个函数的输入。

```javascript
const compose = (f, g) => (a) => f(g(a)) // Definition
const floorAndToString = compose((val) => val.toString(), Math.floor) // Usage
floorAndToString(121.212121) // '121'
```

<div id="continuation" tabindex="-1"></div>

## Continuation

在一个程序执行的任意时刻，尚未执行的代码称为 Continuation。

```javascript
const printAsString = (num) => console.log(`Given ${num}`)
 
const addOneAndContinue = (num, cc) => {
  const result = num + 1
  cc(result)
}
 
addOneAndContinue(2, printAsString) // 'Given 3'
```

Continuation 在异步编程中很常见，当程序需要等待接收数据才能继续执行。一旦接收到数据，请求响应通常会被传递给程序的剩余部分，这个剩余部分就是 Continuation。

```javascript
const continueProgramWith = (data) => {
  // 通过 data 继续执行
}
 
readFileAsync('path/to/file', (err, response) => {
  if (err) {
    // 错误处理
    return
  }
  continueProgramWith(response)
})
```

<div id="purity" tabindex="-1"></div>

## 纯函数 Purity

如果返回值仅由其输入值决定，并且不产生副作用，那么这个函数就是纯函数。

```javascript
const greet = (name) => `Hi, ${name}`
 
greet('Brianne') // 'Hi, Brianne'
```

以下代码不是纯函数：

```javascript
window.name = 'Brianne'
 
const greet = () => `Hi, ${window.name}`
 
greet() // "Hi, Brianne"
```

上述示例的输出基于存储在函数外部的数据…

```javascript
let greeting
 
const greet = (name) => {
  greeting = `Hi, ${name}`
}
 
greet('Brianne')
greeting // "Hi, Brianne"
```

… 而这个示例则是修改了函数外部的状态。

<div id="side-effects" tabindex="-1"></div>

## 副作用 Side effects

函数或表达式如果被认为具有副作用，那么除了返回值之外，它可以与外部可变状态（读取或写入）进行交互。

```javascript
const differentEveryTime = new Date()
```

```javascript
console.log('IO is a side effect!')
```

<div id="idempotent" tabindex="-1"></div>

## 幂等 Idempotent

将一个函数重新应用其结果，如果产生的结果是相同的，那么该函数是幂等的。

A function is idempotent if reapplying it to its result does not produce a different result.

f(f(x)) ≍ f(x)

```javascript
Math.abs(Math.abs(10))
```

```javascript
sort(sort(sort([2, 1])))
```

<div id="point-free-style" tabindex="-1"></div>

## Point-Free Style

定义函数时，没有显式地标识所使用的参数。这种风格通常需要 [currying(柯里化)](#currying) 或 [高阶函数](#higher-order-functions-hof)。 也叫 Tacit programming。

```javascript
// 给定
const map = (fn) => (list) => list.map(fn)
const add = (a) => (b) => a + b
 
// 然后
 
// 非 points-free - `numbers` 是一个明确的参数
const incrementAll = (numbers) => map(add(1))(numbers)
 
// Points-free - `list` 显式地标识
const incrementAll2 = map(add(1))
```

`incrementAll` 明确的使用了参数 `numbers`，所以它是非 points-free 风格。 `incrementAll2` 连接函数与值，并不提及它的参数。所以 **是** points-free 风格.

Point-Free 风格的函数就像平常的赋值，不使用 `function` 或者 `=>`。

<div id="predicate" tabindex="-1"></div>

## 断言 Predicate

断言函数是根据给定值返回 `true`或 `false` 的函数。断言函数的常见用法是作为数组过滤器的回调。

```javascript
const predicate = (a) => a > 2

;[1, 2, 3, 4].filter(predicate) // [3, 4]
```

<div id="contracts" tabindex="-1"></div>

## 约定 Contracts

约定运行时从函数或表达式中指定行为的义务和保证。这就像一组规则，这些规则是由函数或表达式的输入和输出所期望的，并且当违反契约时，通常会抛出错误。

```javascript
// 定义约定 : int -> int
const contract = (input) => {
  if (typeof input === 'number') return true
  throw new Error('Contract violated: expected int -> int')
}
 
const addOne = (num) => contract(num) && num + 1
 
addOne(2) // 3
addOne('some string') // Contract violated: expected int -> int
```

<div id="category" tabindex="-1"></div>

## Category

在范畴论中，范畴是指对象集合及它们之间的态射 (morphism)。在编程中，数据类型作为对象，函数作为态射。

一个有效的范畴遵从以下三个原则：

1. 必有一个 identity 态射，使得 map 一个对象是它自身。`a` 是范畴里的一个对象时，必有一个函数使 `a -> a`。
2. 态射必是可组合的。`a`，`b`，`c` 是范畴里的对象，`f` 是态射 `a -> b`，`g` 是 `b -> c`态射。`g(f(x))` 一定与 `(g • f)(x)` 是等价的。
3. 合成满足结合律。`f • (g • h)` 与 `(f • g) • h` 是等价的。

这些准则是非常抽象的，范畴论对与发现组合的新方法是伟大的。

**进一步阅读**

- [Category Theory for Programmers](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)

<div id="value" tabindex="-1"></div>

## 值 Value

任何可以分配给变量的东西称作值。

```javascript
5
Object.freeze({name: 'John', age: 30}) // `freeze` 强制实现不可变性。
;(a) => a
;[1]
undefined
```

<div id="constant" tabindex="-1"></div>

## Constant

一旦定义不可重新赋值的变量。

```javascript
const five = 5
const john = Object.freeze({name: 'John', age: 30})
```

常量是[引用透明](#referential-transparency)的，因此它们可以被它们所代表的值替代而不影响结果。

对于以上两个常量，以下语句总会返回 true。

```javascript
john.age + five === ({name: 'John', age: 30}).age + (5)
```

<div id="functor" tabindex="-1"></div>

## 函子 Functor

一个实现 `map` 函数的对象，它在运行对象中的每个值以生成一个新对象，时遵守两个规则：

**一致性**

```javascript
object.map(x => x) ≍ object
```

**合成性**

```javascript
object.map(compose(f, g)) ≍ object.map(g).map(f)
```

(`f`, `g` be arbitrary functions)

在 javascript 中一个常见的函子是 `Array` , 因为它遵守因子的两个准则。

```javascript
;[1, 2, 3].map(x => x) // = [1, 2, 3]
```

和

```javascript
const f = x => x + 1
const g = x => x * 2
 
;[1, 2, 3].map(x => f(g(x))) // = [3, 5, 7]
;[1, 2, 3].map(g).map(f)     // = [3, 5, 7]

```

<div id="pointed-functor" tabindex="-1"></div>

## Pointed Functor

一个具有 `of` 函数的对象，它将 *任何* 单独的值放入其中。

ES2015 增加了 `Array.of` ，使数组成为一个 Pointed Functor 。

```javascript
Array.of(1) // [1]
```

<div id="lift" tabindex="-1"></div>

## 提升 Lift

提升就是当你取一个值并将其放入一个像[functor](#pointed-functor)这样的对象。如果你把一个函数提升到一个 [Applicative Functor](#applicative-functor)中，那么你可以使它在那个函子中的值也可以工作。

有些实现提供了一个名为 `lift` 或 `liftA2` 的函数，可以使函数在 函子 上更容易运行。

```javascript
const liftA2 = (f) => (a, b) => a.map(f).ap(b) // 注意这里提升的是 `ap` ，而不是 `map` 。
 
const mult = a => b => a * b
 
const liftedMult = liftA2(mult) // 这个函数现在能在函子上像数组一样工作
 
liftedMult([1, 2], [3]) // [3, 6]
liftA2(a => b => a + b)([1, 2], [3, 4]) // [4, 5, 5, 6]
```

提升一个单参数函数并应用它，那么它和 `map` 做一样的事情。

```javascript
const increment = (x) => x + 1
 
lift(increment)([2]) // [3]
;[2].map(increment) // [3]
```

<div id="referential-transparency" tabindex="-1"></div>

## 引用透明性 Referential Transparency

在不改变程序行为的情况下，一个表达式能够被它的值替代，这被认为是引用透明。

比方说，我们有一个 greet 函数：

```javascript
const greet = () => 'Hello World!'
```

`greet()` 的任何调用可以用 `Hello World!` 代替！因此 greet 被认为是引用透明。

<div id="equational-reasoning" tabindex="-1"></div>

## 等式推理 Equational Reasoning

当应用程序由表达式组成，并且没有副作用时，关于系统的真值可以从各个部分推导出来。

<div id="lambda" tabindex="-1"></div>

## Lambda

一个匿名函数，被当作一个值来对待。

```javascript
;(function (a) {
  return a + 1
})
 
;(a) => a + 1
```

匿名函数通常作为高阶函数的参数

```javascript
;[1, 2].map((a) => a + 1) // [2, 3]
```

可以把 Lambda 赋值给一个变量

```javascript
const add1 = (a) => a + 1
```

<div id="lambda-calculus" tabindex="-1"></div>

## λ演算 Lambda Calculus

数学的一个分支，使用函数创造 [通过计算模型](https://en.wikipedia.org/wiki/Lambda_calculus)

<div id="lazy-evaluation" tabindex="-1"></div>

## 惰性求值 Lazy evaluation

惰性求值是一种按需求值机制，它会延迟对表达式的求值，直到其需要为止。在函数式语言中，这允许像无限列表这样的结构，通常情况下排序重要的命令语言不可用。

```javascript
const rand = function*() {
  while (1 < 2) {
    yield Math.random()
  }
}
```

```javascript
const randIter = rand()
randIter.next() // 每个执行都给出一个随机值，表达式按需求值。
```

<div id="monoid" tabindex="-1"></div>

## Monoid

一个对象拥有一个函数用来连接相同类型的对象。

数值加法是一个简单的 Monoid ：

```javascript
1 + 1 // 2
```

以上示例中，数值是对象而 `+` 是函数。

与另一个值结合而不会改变它的值必须存在，称为 `identity`。

加法的 `identity` 值为 0:

```javascript
1 + 0 // 1
```

需要满足结合律

```javascript
1 + (2 + 3) === (1 + 2) + 3 // true
```

数组的结合也是 Monoid

```javascript
;[1, 2].concat([3, 4]) // [1, 2, 3, 4]
```

identity 的值为空数组 `[]`

```javascript
;[1, 2].concat([]) // [1, 2]
```

identity 与 compose 函数能够组成 monoid

```javascript
const identity = (a) => a
const compose = (f, g) => (x) => f(g(x))
```

`foo` 是只带一个参数的任意函数。

```javascript
compose(foo, identity) ≍ compose(identity, foo) ≍ foo
```

<div id="monad" tabindex="-1"></div>

## Monad

monad是一个具有 [`of`](#pointed-functor) 和 `chain` 函数的对象。`chain` 就像 [`map`](#functor) ， 除了用来铺平嵌套数据。

```javascript
// 实现
Array.prototype.chain = function (f) {
  return this.reduce((acc, it) => acc.concat(f(it)), [])
}
 
// 用法
Array.of('cat,dog', 'fish,bird').chain((a) => a.split(',')) // ['cat', 'dog', 'fish', 'bird']
 
// 对比 map 
Array.of('cat,dog', 'fish,bird').map((a) => a.split(',')) // [['cat', 'dog'], ['fish', 'bird']]
```

在有些语言中，`of` 也称为 `return`，`chain` 也称为 `flatmap` 与 `bind`。

<div id="comonad" tabindex="-1"></div>

## Comonad

拥有 `extract` 与 `extend` 函数的对象。

```javascript
const CoIdentity = (v) => ({
  val: v,
  extract () {
    return this.val
  },
  extend (f) {
    return CoIdentity(f(this))
  }
})
```

从函子中 `extract` (提取) 出一个值。

```javascript
CoIdentity(1).extract() // 1
```

Extend 在 comonad 上运行一个函数。函数应该返回与 comonad 相同的类型。

```javascript
CoIdentity(1).extend((co) => co.extract() + 1) // CoIdentity(2)
```

<div id="applicative-functor" tabindex="-1"></div>

## Applicative Functor

应用函子是具有`ap`函数的对象。 `ap`将对象中的函数应用于同一类型的另一个对象中的值。

```javascript
// 实现
Array.prototype.ap = function (xs) {
  return this.reduce((acc, f) => acc.concat(xs.map(f)), [])
}
 
// 使用示例
;[(a) => a + 1].ap([1]) // [2]
```

如果你有两个对象，并需要对他们的元素执行一个二元函数

```javascript
// 你想合成的数组
const arg1 = [1, 3]
const arg2 = [4, 5]
 
// 合成函数 - 必须为此 curried 柯里化 才能工作
const add = (x) => (y) => x + y
 
const partiallyAppliedAdds = [add].ap(arg1) // [(y) => 1 + y, (y) => 3 + y]
```

由此得到了一个函数数组，并且可以调用 `ap` 函数得到结果：

```javascript
partiallyAppliedAdds.ap(arg2) // [5, 6, 7, 8]
```

## 态射 Morphism

一个变形的函数。

<div id="endomorphism" tabindex="-1"></div>

### 自同态 Endomorphism

输入输出是相同类型的函数。

```javascript
// uppercase :: String -> String
const uppercase = (str) => str.toUpperCase()
 
// decrement :: Number -> Number
const decrement = (x) => x - 1
```

<div id="isomorphism" tabindex="-1"></div>

### 同构 Isomorphism

不用类型对象的变形，保持结构并且不丢失数据。

例如，一个二维坐标既可以表示为数组 `[2, 3]`，也可以表示为对象 `{x: 2, y: 3}`。

```javascript
// 提供函数在两种类型间互相转换
const pairToCoords = (pair) => ({x: pair[0], y: pair[1]})
 
const coordsToPair = (coords) => [coords.x, coords.y]
 
coordsToPair(pairToCoords([1, 2])) // [1, 2]
 
pairToCoords(coordsToPair({x: 1, y: 2})) // {x: 1, y: 2}
```

<div id="setoid" tabindex="-1"></div>

## Setoid

拥有 `equals` 函数的对象。`equals` 可以用来和其它对象比较。

使数组成为一个 setoid:

```javascript
Array.prototype.equals = function (arr) {
  const len = this.length
  if (len !== arr.length) {
    return false
  }
  for (let i = 0; i < len; i++) {
    if (this[i] !== arr[i]) {
      return false
    }
  }
  return true
}
 
;[1, 2].equals([1, 2]) // true
;[1, 2].equals([0]) // false
```

<div id="semigroup" tabindex="-1"></div>

## 半群 Semigroup

一个拥有 `concat` 函数的对象。`concat` 可以连接相同类型的两个对象。

```javascript
;[1].concat([2]) // [1, 2]
```

<div id="foldable" tabindex="-1"></div>

## Foldable

一个拥有 `reduce` 函数的对象。`reduce` 可以把一种类型的对象转化为另一种类型。

```javascript
const sum = (list) => list.reduce((acc, val) => acc + val, 0)
sum([1, 2, 3]) // 6
```

<div id="type-signatures" tabindex="-1"></div>

## 类型签名 Type Signatures

通常，JavaScript 中的函数会包含一些注释，这些注释指明了它们的参数和返回值的类型。

整个社区有很大的差异，但是他们经常遵循以下模式：

```javascript
// functionName :: firstArgType -> secondArgType -> returnType
 
// add :: Number -> Number -> Number
const add = (x) => (y) => x + y
 
// increment :: Number -> Number
const increment = (x) => x + 1
```

如果函数的参数也是函数，那么这个函数需要用括号括起来。

```javascript
// call :: (a -> b) -> a -> b
const call = (f) => (x) => f(x)
```

字符 `a`， `b`，`c`，`d` 用于表示参数可以是任何类型。 以下版本的 `map` 采用将某种类型 `a` 的值转换为另一个类型`b`的函数，这是一个类型为 `a` 的数组，并返回一个类型为 `b` 的数组。

```javascript
// map :: (a -> b) -> [a] -> [b]
const map = (f) => (list) => list.map(f)
```

**进一步阅读**
* [Ramda’s type signatures](https://github.com/ramda/ramda/wiki/Type-Signatures)
* [Mostly Adequate Guide](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch7.html#whats-your-type)
* [What is Hindley-Milner?](http://stackoverflow.com/a/399392/22425) on Stack Overflow



## 代数数据类型 Algebraic data type

由其他类型组合在一起的复合类型。两种常见的代数类型是 [sum](#sum-type) 和 [product](#product-type) 。

<div id="sum" tabindex="-1"></div>

### Sum 类型

Sum 类型是将两种类型的组合合并成另一种类型。它之所以被称为 sum ，是因为结果类型中可能的值的数量是输入类型的总和。

JavaScript 没有这样的类型，但是我们可以使用 `Set` 来假装：

```javascript
// 想象一下，在这里我们不能设置只能具有这些值的类型
const bools = new Set([true, false])
const halfTrue = new Set(['half-true'])
 
// 弱逻辑类型包含 bools 和 halfTrue 值的总和
const weakLogicValues = new Set([...bools, ...halfTrue])
```

Sum 类型有时称为联合类型 (Union Type)， discriminated unions, 或 tagged unions。

JS中有 [couple](https://github.com/paldepind/union-type) [libraries](https://github.com/puffnfresh/daggy) 可以帮助定义和使用联合类型。

Flow 包含 [union types](https://flow.org/en/docs/types/unions/) ，TypeScript具有[Enums](https://www.typescriptlang.org/docs/handbook/enums.html) 以提供相同的角色。

<div id="product-type" tabindex="-1"></div>

### Product type

**product** 类型将类型合并在一起，您可能更熟悉以下方面：

```javascript
// point :: (Number, Number) -> {x: Number, y: Number}
const point = (x, y) => ({x: x, y: y})
```

它被称为 product ，因为数据结构的总可能值是不同值的乘积。许多语言具有元组类型，它是产品类型的最简单的公式。

参见 [Set theory](https://en.wikipedia.org/wiki/Set_theory).

<div id="option" tabindex="-1"></div>

## Option

Option 是一种[sum type](#sum-type) ，它有两种情况，`Some` 或者 `None`。

Option 对于组合可能不返回值的函数很有用。

```javascript
// 定义
 
const Some = (v) => ({
  val: v,
  map (f) {
    return Some(f(this.val))
  },
  chain (f) {
    return f(this.val)
  }
})
 
const None = () => ({
  map (f) {
    return this
  },
  chain (f) {
    return this
  }
})
 
// maybeProp :: (String, {a}) -> Option a
const maybeProp = (key, obj) => typeof obj[key] === 'undefined' ? None() : Some(obj[key])
```

使用 `chain` 可以序列化返回 `Option` 的函数。

```javascript
// getItem :: Cart -> Option CartItem
const getItem = (cart) => maybeProp('item', cart)
 
// getPrice :: Item -> Option Number
const getPrice = (item) => maybeProp('price', item)
 
// getNestedPrice :: cart -> Option a
const getNestedPrice = (cart) => getItem(obj).chain(getPrice)
 
getNestedPrice({}) // None()
getNestedPrice({item: {foo: 1}}) // None()
getNestedPrice({item: {price: 9.99}}) // Some(9.99)
```

在其它的一些地方，`Option` 也称为 `Maybe`，`Some` 也称为 `Just`，`None` 也称为 `Nothing`。

## JavaScript 中的函数式编程库

- [mori](https://github.com/swannodette/mori)
- [Immutable](https://github.com/facebook/immutable-js/)
- [Ramda](https://github.com/ramda/ramda)
- [ramda-adjunct](https://github.com/char0n/ramda-adjunct)
- [Folktale](http://folktalejs.org/)
- [monet.js](https://cwmyers.github.io/monet.js/)
- [lodash](https://github.com/lodash/lodash)
- [Underscore.js](https://github.com/jashkenas/underscore)
- [Lazy.js](https://github.com/dtao/lazy.js)
- [maryamyriameliamurphies.js](https://github.com/sjsyrek/maryamyriameliamurphies.js)
- [Haskell in ES6](https://github.com/casualjavascript/haskell-in-es6)
- [Sanctuary](https://github.com/sanctuary-js/sanctuary)