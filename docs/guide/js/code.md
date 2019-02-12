# 代码规范

统一团队的编码规范，有助于代码的维护。本章是传统意义上的 `Style Guideline`，目的是统一一些相对主观化的代码风格。

## 单行代码块

在单行代码块中使用空格。

```js
// bad
function foo() {return true}
if (foo) {bar = 0}

// good
function foo() { return true }
if (foo) { bar = 0 }
```

## 大括号风格

在编程过程中，大括号风格与缩进风格紧密联系，用来描述大括号相对代码块位置的方法有很多。在 JavaScript 中，主要有三种风格，如下：

- One True Brace Style

  ```js
  if (foo) {
    bar()
  } else {
    baz()
  }
  ```

- Stroustrup

  ```js
  if (foo) {
    bar()
  }
  else {
    baz()
  }
  ```

- Allman

  ```js
  if (foo)
  {
    bar()
  }
  else
  {
    baz()
  }
  ```

咱们团队约定使用 `One True Brace Style` 风格。

## 变量命名

当命名变量时，主流分为驼峰式命名（variableName）和下划线命名（variable_name）两大阵营。

**团队约定使用驼峰式命名。**

## 拖尾逗号

添加拖尾逗号，eslint: `comma-dangle`

最后一个元素或属性与闭括号 `]` 或 `}` 在不同行的时候使用拖尾逗号，在同一行时不使用拖尾逗号。

这个将造成更清洁的 git 扩展差异。另外，像 Babel 这样的编译器，会在转换后的代码中删除额外的拖尾逗号，这意味着你不必担心在浏览器中后面的[拖尾逗号问题](https://github.com/airbnb/javascript/blob/es5-deprecated/es5/README.md#commas)。

```js
// bad - 没有尾随逗号的 git 差异
const hero = {
     firstName: 'Florence',
-    lastName: 'Nightingale'
+    lastName: 'Nightingale',
+    inventorOf: ['coxcomb chart', 'modern nursing']
}

// good - 有尾随逗号的 git 差异
const hero = {
     firstName: 'Florence',
     lastName: 'Nightingale',
+    inventorOf: ['coxcomb chart', 'modern nursing'],
}
```

```js
// bad
const hero = {
  firstName: 'Dana',
  lastName: 'Scully'
}

const heroes = [
  'Batman',
  'Superman'
]

// good
const hero = {
  firstName: 'Dana',
  lastName: 'Scully',
}

const heroes = [
  'Batman',
  'Superman',
]

// bad
function createHero(
  firstName,
  lastName,
  inventorOf
) {
  // does nothing
}

// good
function createHero(
  firstName,
  lastName,
  inventorOf,
) {
  // does nothing
}

// good (注意逗号不能出现在 "rest" 元素后边)
function createHero(
  firstName,
  lastName,
  inventorOf,
  ...heroArgs
) {
  // does nothing
}

// bad
createHero(
  firstName,
  lastName,
  inventorOf
)

// good
createHero(
  firstName,
  lastName,
  inventorOf,
)

// good (注意逗号不能出现在 "rest" 元素后边)
createHero(
  firstName,
  lastName,
  inventorOf,
  ...heroArgs
)
```

## 逗号空格

逗号前后的空格可以提高代码的可读性，团队约定在逗号后面使用空格，逗号前面不加空格。eslint: `comma-spacing`

```js
// bad
let foo = 1,bar = 2
const arr = [1 , 2]

// good
let foo = 1, bar = 2
const arr = [1, 2]
```

## 逗号风格

逗号分隔列表时，在 JavaScript 中主要有两种逗号风格：

- 标准风格，逗号放置在当前行的末尾。
- 逗号前置风格，逗号放置在下一行的开始位置。

**团队约定标准风格。**

```js
// bad
let foo = 1
,
bar = 2

let foo = 1
, bar = 2

const foo = [
  'name'
  , 'age'
]

// good
let foo = 1,
    bar = 2

const foo = [
  'name',
  'age',
]
```

## 计算属性的空格

**团队约定在计算属性内，禁止使用空格**。eslint: `computed-property-spacing`

```js
// bad
obj[foo ]
obj[ 'foo']
var x = {[ b ]: a}
obj[foo[ bar ]]

// good
obj[foo]
obj['foo']
var x = { [b]: a }
obj[foo[bar]]

```

## 拖尾换行

在非空文件中，存在拖尾换行是一个常见的 `UNIX` 风格，它的好处是可以方便在串联和追加文件时不会打断 `Shell` 的提示。在日常的项目中，保留拖尾换行的好处是，可以减少版本控制时的代码冲突。

```js
// bad
function func() {
  // do something
}

// good
function func() {
  // do something
}
  // 此处是新的一行
```

## 函数调用

为了避免语法错误，团队约定在函数调用时，禁止使用空格

```js
// bad
fn ()
fn
()

// good
fn()
```

## 缩进

代码保持一致的缩进，是作为工程师的职业素养。但缩进用两个空格，还是四个空格，是用 `Tab` 还是空格呢？这样的争论太多了，本规范结合了市面上优秀的开源项目，姑且约定使用 `空格` 来缩进，而且缩进使用两个空格。

那是不是不能使用 `Tab` 进行缩进了呢？我们可以通过配置 `.editorconfig` ，将 `Tab` 自动转换为空格。

## 对象字面量的键值缩进

团队约定对象字面量的键和值之间不能存在空格，且要求对象字面量的冒号和值之间存在一个空格。

```js
// bad
const obj = { 'foo' : 'haha' }

// good
const obj = { 'foo': 'haha' }
```

## 构造函数首字母大写

在 JavaScript 中 `new` 操作符用来创建某个特定类型的对象的一个实例，该类型的对象是由一个构造函数表示的。由于构造函数只是常规函数，唯一区别是使用 `new` 来调用的。所以团队约定构造函数的首字母要大小，以此来区分构造函数和普通函数。

```js
// bad
const fooItem = new foo()

// good
const fooItem = new Foo()
```

## 构造函数的参数

在 JavaScript 中，通过 `new` 调用构造函数时，如果不带参数，可以省略后面的圆括号。但这样会造成整体的代码风格不一致，所以团队约定使用圆括号。

```js
// bad
const person = new Person

// good
const person = new Person()
```

## 链式调用

链式调用如果放在同一行，往往会造成代码的可读性差，但有些时候，短的链式调用并不会影响美观。所以本规范约定一行最多只能有四个链式调用，超过就要求换行。

## 空行

空行对于分离代码逻辑有帮助，但过多的空行会占据屏幕的空间，影响可读性。团队约定最大连续空行数为2。

```js
// bad
let a = 1



let b = 2

// good
let a = 1

let b = 2

```

## 链式赋值

链式赋值容易造成代码的可读性差，所以团队约定禁止使用链式赋值。

```js
// bad
let a = b = c = 1

// good
let a = 1
let b = 1
let c = 1
```

## 变量声明

JavaScript 允许在一个声明中，声明多个变量。团队约定在声明变量时，一个声明只能有一个变量。

```js
// bad
let a, b, c

// good
let a
let b
let c
```

## 分号

JavaScript 在所有类 C 语言中是比较独特的，它不需要在每个语句的末尾添加分号。在很多情况下，JavaScript 引擎可以确定一个分号应该在什么位置然后自动添加它。此特征被称为自动分号插入（ASI）,被认为是 JavaScript 中较有争议的特征。

本规范推荐不使用分号，因为我们认为好的工程师应该知道什么时候该加，什么时候不该加。

如果下一行的第一个token是 ` (` ,` [` , `/` , `+` , `-` 这五个字符之一的话， Javascript 将不会自动在上一行句尾加上分号。

## 代码块空格

一致性是任何风格指南的重要组成部分。虽然在哪里放置块的开括号纯属个人偏好，但在整个项目中保持一致。不一致的风格将会分散读者阅读代码的注意力。

团队约定代码块前要添加空格。

```js
// bad
if (a){
  b()
}

// good
if (a) {
  b()
}
```

## 操作符的空格

团队约定操作符前后都需要添加空格。

```js
// bad
let sum = 1+2

// good
let sum = 1 + 2
```

