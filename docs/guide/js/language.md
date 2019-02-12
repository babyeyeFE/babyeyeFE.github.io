# 语言规范

JavaScript 是一种客户端脚本语言，这里列出了编写 JavaScript 时需要遵守的规则。

## 类型

**原始值**: 当你访问一个原始类型的时候，你可以直接使用它的值。

- string
- number
- boolean
- null
- undefined
- symbol

```js
const foo = 1
let bar = foo

bar = 9

console.log(foo, bar) // => 1, 9
```
标识符不能完全被支持，因此在针对不支持的浏览器或者环境时不应该使用它们。

**复杂类型**: 当你访问一个复杂类型的时候，你需要一个值的引用。

- object
- array
- function

```js
const foo = [1, 2]
const bar = foo

bar[0] = 9

console.log(foo[0], bar[0]) // => 9, 9
```

## 引用

`const` 和 `let` 都是块级作用域，`var` 是函数级作用域。

- 使用 `const` 定义你的所有引用；避免使用 `var` 。eslint: `preser-const`，`no-const-assign`

```js
// bad
var a = 1
var b = 2

// good
const a = 1
const b = 2
```

- 如果引用是可变动的，则使用 `let`。 eslint: `no-var`

```js
// bad
var count = 1
if (const < 10) {
  count += 1
}

// good
let count = 1
if (count < 10) {
  count += 1
}
```

- 注意，`let` 和 `const` 都是块级范围的。

```js
// const 和 let 只存在于他们定义的块中
{
  let a = 1
  const b  = 2
}

console.log(a) // ReferenceError
console.log(b) // ReferenceError
```

## 对象

- 请使用字面量值创建对象。eslint: `no-new-object`

```js
// bad
const isFalse = new Boolean(false)
const obj = new Object()

// good
const isFalse = false
const obj = {}
```

- 别使用保留字作为对象的键值，这样在 IE8 下不会运行。

```js
// bad
const a = {
  default: {}, // default 是保留字
  common: {},
}

// good
const a = {
  defaults: {},
  common: {},
}
```

- 在创建具有动态属性名称的对象时使用计算属性名。

```js
function getKey(key) {
  return `a key named ${key}`
}
// bad
const obj = {
  id: 5,
  name: 'San Francisco',
}
obj[getKey('enabled')] = true

// good
const obj = {
  id: 5,
  name: 'San Francisco',
  [getKey('enabled')]: true,
}
```

- 请使用对象方法的简写方式。eslint: `object-shorthand`

```js
// bad
const atom = {
  value: 1,
  addValue: function(value) {
    return atom.value + value
  },
}

// good
const atom = {
  value: 1,
  addValue(value) {
    return atom.value + value
  }
}
```

- 请使用对象属性值的简写方式。eslint: `object-shorthand`

```js
const team = 'BabyeyeFE'
// bad
const obj = {
  team: team,
}

// good
const obj = {
  team,
}
```

- 对象属性值的简写方式要和声明式的方式分组。

```js
const job = 'FrontEnd'
const department = 'BabyeyeFE'

// bad
const person = {
  gender: 'male',
  job,
  age: 18,
  department,
}

// good
const person = {
  job,
  department,
  gender: 'male',
  age: 18,
}
```

- 只使用引号标注无效标识符的属性。eslint: `quote-props`

  总的来说，我们认为这样更容易阅读。它提升了语法高亮显示，并且更容易通过许多 JS 引擎优化。

```js
// bad
const bad = {
  'foo': 3,
  'bar': 4,
  'data-blah': 5,
}

// good
const good = {
  foo: 3,
  bar: 4,
  'data-blah': 5,
}
```

- 不能直接调用 `Object.prototype` 的方法，如： `hasOwnProperty`、`propertyIsEnumerable` 和 `isPrototypeOf`。

  为什么? 这些方法可能被一下问题对象的属性追踪 - 相应的有 `{ hasOwnProperty: false }` - 或者，对象是一个空对象 (`Object.create(null)`)。

```js
// bad
console.log(object.hasOwnProperty(key))

// good
console.log(Object.prototype.hasOwnProperty.call(object, key))

// best
const has = Object.prototype.hasOwnProperty // 在模块范围内的缓存中查找一次
/* or */
import has from 'has' // https://www.npmjs.com/package/has
// ...
console.log(has.call(object, key))
```

- 更趋向于使用对象扩展操作符，而不是用 `Object.assign` 浅拷贝一个对象。使用对象的 `rest` 操作符来获得一个具有某些属性的新对象。

```js
// bad
const original = { a: 1, b: 2 }
const copy = Object.assign({}, original, { c: 3 }) // copy => { a: 1, b: 2, c: 3 }

// good
const original = { a: 1, b: 2 }
const copy = { ...original, c: 3 } // copy => { a: 1, b: 2, c: 3 }
const { a, ...noA } = copy // noA => { b: 2, c: 3 }
```

## 数组

- 使用字面量值创建数组。eslint: `no-array-constructor`

```js
// bad
const items = new Array()

// good
const items = []
```

- 使用 [Array#push](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push) 取代直接赋值来给数组添加元素。

```js
const items = []

// bad
items[items.length] = 'test'

// good
items.push('test')
```

- 使用拓展运算符 `...` 复制数组。

```js
// bad
const items = [1, 2, 3]
const itemsCopy = []
const len = items.length
let i

for (i = 0; i < len; i++) {
  itemsCopy[i] = items[i]
}

// good
itemsCopy = [...items]
```

- 将一个类数组对象转换成一个数组，使用展开方法 `...` 代替 	[Array.from](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/from)。

```js
const foo = document.querySelectorAll('.foo')

// good
const nodes = Array.from(foo)

// best
const nodes = [...foo]
```

- 对于对迭代器的映射，使用 [Array.from](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/from) 代替展开方法 `...` ，因为它避免了创建中间数组。

```js
// bad
const baz = [...foo].map(bar)

// good
const baz = Array.from(foo, bar)
```

- 在数组回调方法中使用 return 语句。 如果函数体由一个返回无副作用的表达式的单个语句组成，那么可以省略返回值。 eslint: [`array-callback-return`](https://eslint.org/docs/rules/array-callback-return)

```js
// good
[1, 2, 3].map((x) => {
  const y = x + 1
  return x * y
})

// good
[1, 2, 3].map(x => x + 1)

// bad - 没有返回值，意味着在第一次迭代后 `acc` 没有被定义
[[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
  const flatten = acc.concat(item)
  acc[index] = flatten
})

// good
[[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
  const flatten = acc.concat(item)
  acc[index] = flatten
  return flatten
})

// bad
inbox.filter((msg) => {
  const { subject, author } = msg
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee'
  } else {
    return false
  }
})

// good
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee'
  }

  return false
})
```

## 解构赋值

- 当需要使用对象的多个属性时，请使用解构赋值。eslint: `prefer-destructuring`

  解构可以避免为这些属性创建临时引用。

```js
// bad
function getFullName(user) {
  const firstName = user.firstName
  const lastName = user.lastName
  return `${firstName} ${lastName}`
}

// good
function getFullName(user) {
  const { firstName, lastName } = user
  return `${firstName} ${lastName}`
}

// best
function getFullName({ firstName, lastName }) {
  return `${firstName} ${lastName}`
}
```

- 当需要使用数组的多个值时，请同样使用解构赋值。eslint: `prefer-destructuring`

```js
const arr = [1, 2, 3, 4]

// bad
const first = arr[0]
const second = arr[1]

// good
const [first, second] = arr
```

- 函数需要回传多个值时，请使用对象的解构，而不是数组的解构。

  你可以随时添加新的属性或者改变属性的顺序，而不用修改调用方。

```js
// bad
function doSomething() {
  // 处理代码...
  return [top, right, bottom, left]
}

// 如果是数组解构，那么在调用时就需要考虑数据的顺序。
const [top, xx, xxx, left] = doSomething()

// good
function doSomething() {
  // 处理代码...
  return { top, right, bottom, left }
}

// 此时不需要考虑数据的顺序
const { top, left } = doSomething()
```

## 字符串

- 字符串统一使用单引号的形式 `''`。eslint: `quotes`

```js
// bad
const team = "BabyeyeFE"

// good
const team = 'BabyeyeFE'
```

- 程序化生成字符串时，请使用模板字符串。eslint: `prefer-template` `template-curly-spacing`

  字符串模板提供了一种可读的、简洁的语法，具有正确的换行和字符串插值特性。

```js
const test = 'test'

// bad
const str = ['a', 'b', test].join()

// bad
const str = 'a' + 'b' + test

// good
const str = `ab${test}`
```

- 不要在字符串上使用 `eval()`，eslint: `no-eval`
- 不要转义字符串中不必要的字符。 eslint: `no-useless-escape`

```js
// bad
const foo = '\'this\' \i\s \"quoted\"'

// good
const foo = '\'this\' is "quoted"'
const foo = `my name is ${name}`
```

## 函数

- 请使用函数声明，而不是函数表达式（讨论）

```js
// bad
const foo = function() {
  // do something
}

// good
function foo() {
  // do something
}
```

- 不要在非函数代码块中声明函数。**注意：** ECMA-262 将 `block` 定义为语句列表。函数声明不是语句。

```js
// bad
if (currentUser) {
  function test() {
    console.log('Nope')
  }
}

// good
let test
if (currentUser) {
  test = () => {
    console.log('Yep')  
  }
}
```

- 永远不要定义一个参数为 `arguments`。这将会优先于每个函数给定范围的 `arguments` 对象。

```js
// bad
function foo(name, options, arguments) {
  // ...
}

// good
function foo(name, options, args) {
  // ...
}
```

- 不要使用 `arguments`，选择使用 rest 语法 `...` 代替。eslint: `prefer-rest-params`

  `arguments` 只是一个类数组，而 `...` 是一个真正的数组。

```js
// bad
function concatenateAll() {
  const args = Array.prototype.slice.call(arguments)
  return args.join('')
}

// good
function concatenateAll(...args) {
  return args.join('')
}
```

- 使用默认参数语法，而不是改变函数参数

```js
// bad
function handleThings(opts) {
  opts = opts || {}
  // ...
}

// good
function handleThings(opts = {}) {
  // ...
}
```

- 避免使用默认参数的副作用。

```js
var b = 1
// bad
function count(a = b++) {
  console.log(a)
}
count()  // 1
count()  // 2
count(3) // 3
count()  // 3
```

- 总是把默认参数放在最后。

```js
// bad
function handleThings(opts = {}, name) {
  // ...
}

// good
function handleThings(name, opts = {}) {
  // ...
}
```

- 永远不要使用函数构造器来创建一个新函数。eslint: `no-new-func`

  以这种方式创建一个函数将对于一个类似于 `eval()` 的字符串进行计算，这将打开漏洞。

```js
// bad
const add = new Function('a', 'b', 'return a + b')

// still bad
const subtract = Function('a', 'b', 'return a - b')
```

- 不要重新赋值参数。eslint: `no-param-reassign`

  重新赋值参数会导致意外的行为，尤其是在访问 `arguments` 对象的时候。它还可能导致性能优化问题，尤其是在 V8 中。

```js
// bad
function f1(a) {
  a = 1
  // ...
}

function f2(a) {
  if (!a) {
    a = 1
  }
  // ...
}

// good
function f3(a) {
  const b = a || 1
  // ...
}

function f4(a = 1) {
  // ...
}
```

- 优先使用扩展运算符 `...` 来调用可变参数函数。eslint: `prefer-spread`

  它更加干净，你不需要提供上下文，并且你不能轻易的使用 `apply` 来 `new`。

```js
// bad
const x = [1, 2, 3, 4, 5]
console.log.apply(console, x)

// good
const x = [1, 2, 3, 4, 5]
console.log(...x)

// bad
new (Function.prototype.bind.apply(Date, [null, 2019, 1, 24]))

// good
new Date(...[2019, 1, 24])
```

## 箭头函数

- 当你必须使用匿名函数时（当传递内联函数时），使用箭头函数。eslint: `prefer-arrow-callback` `arrow-spacing`。

  它创建了一个在 `this` 上下文中执行的函数版本，它通常是你想要的，并且是一个更简洁的语法。

  如果你有一个相当复杂的函数，你可以把这个逻辑转移到它自己的命名函数表达式中。

```js
// bad
[1, 2, 3].map(function(x) {
  const y = x + 1
  return x * y
})

// good
[1, 2, 3].map(x => {
  const y = x + 1
  return x * y
})
```

- 如果函数体包含一个单独的语句，返回一个没有副作用的 `expression`，省略括号并使用隐式返回。否则，保留括号并使用 `return` 语句。eslint: `arrow-parens`、`arrow-body-style`。

  多个函数被链接在一起时，提高可读性。

```js
// bad
[1, 2, 3].map(number => {
  const nextNumber = number + 1
  `A string containing the ${nextNumber}`
})

// good
[1, 2, 3].map(number => `A string containing the ${number}`)

// good
[1, 2, 3].map(number => {
  const nextNumber = number + 1
  return `A string containing the ${nextNumber}`
})

// good
[1, 2, 3].map((number, index) => ({
  [index]: number,
}))
```

- 如果你的函数接收一个参数，则可以不用括号，省略括号。否则，为了保证清晰和一致性，需要在参数周围加上括号。注意：总是使用括号是可以接受的，在这种情况下，我们使用 `always` 来配置 eslint: `arrow-parens`。

```js
// bad
[1, 2, 3].map((x) => x * x)

// good
[1, 2, 3].map(x => x * x)

// good
[1, 2, 3].map(number => (
  `A long string with the ${number}. It's so long that we don't want it to take up space on the .map line`
))
```

## 原型

- 使用 `class`，避免直接操作 `prototype`。

```js
// bad
function Queue(contents = []) {
  this._queue = [...contents]
}
Queue.prototype.pop = function() {
  const value = this._queue[0]
  this._queue.splice(0, 1)
  return value
}

// good
class Queue {
  constructor(contents = []) {
    this._queue = [...contents]
  }
  
  pop() {
    const value = this._queue[0]
    this._queue.splice(0, 1)
    return value
  }
}
```

## 模块

- 使用标准的 ES6 模块语法 `import` 和 `export`

```js
// bad
const AirbnbStyleGuide = require('./AirbnbStyleGuide')
module.exports = AirbnbStyleGuide.es6

// ok
import AirbnbStyleGuide from './AirbnbStyleGuide'
export default AirbnbStyleGuide.es6

// best
import { es6 } from './AirbnbStyleGuide'
export default es6

```

- 将所有的 `import` 语句放在所有非导入语句的上边。eslint: `import/first`

  由于所有的 `import` 都被提前，保持他们在顶部是为了防止意外发生。

```js
// bad
import foo from 'foo'
foo.init()
import bar from 'bar'

// good
import foo from 'foo'
import bar from 'bar'
foo.init()
```

- 多行导入应该像多行数组和对象一样缩进。

```js
// bad
import { longNameA, longNameB, longNameC, longNameD, longNameE } from 'path'

// good
import {
  longNameA,
  longNameB,
  longNameC,
  longNameD,
  longNameE,
} from 'path'
```

## 对象属性

- 访问属性时使用 `.` 符号。eslint: `dot-notation`

```js
const joke = {
  name: 'haha',
  age: 18,
}
// bad
const name = joke['name']

// good
const name = joke.name
```

## 变量

- 使用 `const` 或者 `let` 来定义变量。如果没有写关键字，变量就会暴露在全局上下文中，这样很可能会和现有变量冲突，另外，也很难明确该变量的作用域是什么。这里推荐使用 `const` 来声明变量，我们需要避免全局命名空间的污染。

```js
// bad
demo = new Demo()

// good
const demo = new Demo()
```

- 使用 `const` 或者 `let` 声明每一个变量。eslint: `one-var`

```js
// bad
const items = getItems(),
      goSportsTeam = true,
      dragonball = 'z'

// good
const items = getItems()
const goSportsTeam = true
const dragonball = 'z'
```

- 将所有的 `const` 和 `let` 分组

```js
// bad
let i
const items = getItems()
let dragonball
const goSportsTeam = true
let len

// good
const goSportsTeam = true
const items = getItems()
let dragonball
let i
let length
```

## 提升

- `var` 存在变量提升的情况，即 `var` 声明会被提升至该作用域的顶部，但是他们的赋值并不会。而 `const` 和 `let` 并不存在这种情况，他们被赋予了 [Temporal Dead Zones, TDZ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#Temporal_dead_zone_and_errors_with_let)

```js
function example() {
  console.log(notDefined)   // => throws a ReferenceError
}

function example() {
  console.log(declareButNotAssigned)  // => undefined
  var declaredButNotAssigned = true
}

function example() {
  let declaredButNotAssigned
  console.log(declaredButNotAssigned)   // => undefined
  declaredButNotAssigned = true
}

function example() {
  console.log(declaredButNotAssigned)   // => throws a ReferenceError
  console.log(typeof declaredButNotAssigned)  // => throws a ReferenceError
  const declaredButNotAssigned = true
}
```

- 匿名函数的变量名会提升，但函数内容不会

```js
function example() {
  console.log(anonymous) // => undefined
    
  anonymous() // => TypeError anonymous is not a function
  
  var anonymous = function() {
    console.log('test') 
  }
}
```

- 命名的函数表达式的变量名会被提升，但函数名和函数内容并不会。

```js
function example() {
  console.log(named) // => undefined

  named() // => TypeError named is not a function

  superPower() // => ReferenceError superPower is not defined

  var named = function superPower() {
    console.log('Flying')
  }
}

// 当函数名和变量名相同时也是如此。
function example() {
  console.log(named) // => undefined

  named() // => TypeError named is not a function

  var named = function named() {
    console.log('named')
  }
}
```

- 函数声明提升其名称和函数体

```js
function example() {
  superPower() // => Flying
  
  function superPower() {
    console.log('Flying')
  }
}
```

## 比较运算符和等号

- 使用 `===` 和 `!==` 而不是 `==` 和 `!=`。 eslint: `eqeqeq`
- 对于布尔值使用简写，但是对于字符串和数字进行显示比较

```js
// bad
if (isValid === true) {
  // ...
}

// good
if (isValid) {
  // ...
}

// bad
if (name) {
  // ...
}

// good
if (name !== '') {
  // ...
}

// bad
if (collection.length) {
  // ...
}

// good
if (collection.length > 0) {
  // ...
}
```

- 三元表达式不应该嵌套，通常是单行表达式。eslint: `no-nested-ternary`

```js
// bad
const foo = maybe1 > maybe2
  ? 'bar'
  : value1 > value2 ? 'baz' : null

// 分离为两个三元表达式
const maybeNull = value1 > value2 ? 'baz' : null

// better
const foo = maybe1 > maybe2
  ? 'bar'
  : maybeNull
// best
const foo = maybe1 > maybe2 ? 'bar' : maybeNull
```

- 避免不必要的三元表达式。eslint: `no-unneeded-ternary`

```js
// bad
const foo = a ? a : b
const bar = c ? true : false
const baz = c ? false : true

// good
const foo = a || b
const bar = !!c
const baz = !c
```

## 块

- 当有多行代码块的时候，使用大括号包裹。eslint: `nonblock-statement-body-position`

```js
// bad
if (test)
  return false

// good
if (test) return false

// good
if (test) {
  return false
}

// bad
function foo() { return false }

// good
function foo() {
  return false
}
```

- 如果你使用的是 `if` 和 `else` 的多行代码块，则将 `else` 语句放在`if` 块闭括号同一行的位置。eslint: `brace-style`。

```js
// bad
if (test) {
  thing1()
  thing2()
}
else {
  thing3()
}

// good
if (test) {
  thing1()
  thing2()
} else {
  thing3()
}
```

- 如果一个 `if` 块总是执行一个 `return ` 语句，那么接下来的 `else` 块就没有必要了。如果一个包含 `return` 语句的 `else if` 块，在一个包含了 `return` 语句的 `if` 块之后，那么可以拆成多个 `if` 块。eslint: `no-else-return`。

```js
// bad
function foo() {
  if (x) {
    return x
  } else {
    return y
  }
}

// bad
function cats() {
  if (x) {
    return x
  } else if (y) {
    return y
  }
}

// bad
function dogs() {
  if (x) {
    return x
  } else {
    if (y) {
      return y
    }
  }
}

// good
function foo() {
  if (x) {
    return x
  }
  return y
}

// good
function cats() {
  if (x) {
    return x
  }
  if (y) {
    return y
  }
}

// good
function dogs(x) {
  if (x) {
    if (z) {
      return y
    }
  } else {
    return z
  }
}
```

## 注释

- 使用 `/** ... */` 来进行多行注释。

```js
// bad
// make() return a new element
// based on the passed in tag name
//
// @param {String} tag
// @return {Element} element
function make(tag) {
  // ...
  return element
}

// good
/**
 * make() returns a new element
 * based on the passed-in tag name
 */
function make(tag) {
  // ...
  return element
}
```

- 使用 `//` 进行单行注释。将单行注释放在需要注释的行的上方新行。在注释之前放一个空行，除非它在块的第一行。

```js
// bad
const active = true // is current tab

// good
// is current tab
const active = true

// bad
function getType() {
  console.log('fetching type...')
  // set the default type to 'no type'
  const type = this.type || 'no type'
  
  return type
}

// good
function getType() {
  console.log('fetching type...')
    
  // set the default type to 'to type'
  const type = this.type || 'no type'
  
  return type
}

// also good
function getType() {
  // set the default type to 'no type'
  const type = this.type || 'no type'
  
  return type
}
```

- 用一个空格开始所有的注释，使它更容易阅读。eslint: `spaced-comment`

```js
// bad
//is current tab
const active = true

// good
// is current tab
const active = true

// bad
/**
 *make() returns a new element
 *based on the passed-in tag name
 */
function make(tag) {
  // ...
  return element
}

// good
/**
 * make() returns a new element
 * based on the passed-in tag name
 */
function make(tag) {
  // ...
  return element
}
```

- 使用 `FIXME` 或者 `TODO` 开始你的注释可以帮助其他开发人员快速了解，如果你提出一个需要重新审视的问题，或者你对需要实现的问题提出的解决方案。这些不同于其他评论，因为他们是可操作的。这些行为是 `FIXME: -- 需要解决这个问题` 或者 `TODO: -- 需要被实现`。
- 使用 `// FIXME:` 注释一个问题。

```js
class Calculator extends Abacus {
  constructor() {
    super()

    // FIXME: 这里不应该使用全局变零
    total = 0
  }
}
```

- 使用 `// TODO:` 注释解决问题的方法。

```js
class Calculator extends Abacus {
  constructor() {
    super()

    // TODO: total 应该由一个 param 的选项配置
    this.total = 0
  }
}
```

