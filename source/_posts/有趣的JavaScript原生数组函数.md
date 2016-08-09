---
title: 有趣的JavaScript原生数组函数
tags: [javascript]
date: 2016/08/09
---

在JavaScript中，创建数组可以使用Array构造函数，或者使用数组直接量[],后者是首选方法。Array对象继承自Object.prototype,对数组执行typeof操作符返回object而不是array。然而，[] instanceof Array也返回true。也就是说，类数组对象的实现更复杂，例如strings对象、arguments对象，arguments对象不是Array的实例，但有length属性，并能通过索引取值，所以能像数组一样进行循环操作。

在本文中，我将复习一些数组原型的方法，并探索这些方法的用法。

- 循环：.forEach
- 判断：.some和.every
- 区分.join和.concat
- 栈和队列的实现：.pop, .push, .shift,和 .unshift
- 模型映射：.map
- 查询：.filter
- 排序：.sort
- 计算：.reduce和.reduceRight
- 复制：.slice
- 强大的.splice
- 查找：.indexOf
- 操作符：in
- 走近.reverse

### 循环：.forEach

这是JavaScript中最简单的方法，但是IE7和IE8不支持此方法。

.forEach 有一个回调函数作为参数，遍历数组时，每个数组元素均会调用它，回调函数接受三个参数：

- value:当前元素
- index:当前元素的索引
- array:要遍历的数组

此外，可以传递可选的第二个参数，作为每次函数调用的上下文（this）.

```
['_', 't', 'a', 'n', 'i', 'f', ']'].forEach(function (value, index, array) {
    this.push(String.fromCharCode(value.charCodeAt() + index + 2))
}, out = [])
out.join('')
// <- 'awesome'
```

后文会提及.join，在这个示例中，它用于拼接数组中的不同元素，效果类似于out[0] + ” + out[1] + ” + out[2] + ” + out[n]。

不能中断.forEach循环，并且抛出异常也是不明智的选择。幸运的事我们有另外的方式来中断操作。

### 判断：.some和.every

如果你用过.NET中的枚举，这两个方法和.Any(x => x.IsAwesome) 、 .All(x => x.IsAwesome)类似。
和.forEach的参数类似，需要一个包含value，index，和array三个参数的回调函数，并且也有一个可选的第二个上下文参数。MDN对.some的描述如下：

> some将会给数组里的每一个元素执行一遍回调函数，直到回调函数返回true。如果找到目标元素，some立即返回true，否则some返回false。回调函数只对已经指定值的数组索引执行；它不会对已删除的或未指定值的元素调用。

```
max = -Infinity
satisfied = [10, 12, 10, 8, 5, 23].some(function (value, index, array) {
    if (value > max) max = value
    return value < 10
})
console.log(max)
// <- 12
satisfied
// <- true
```

注意，当回调函数的value < 10时，中断函数循环。.every的运行原理和.some类似，但回调函数是返回false而不是true。

### 区分.join和.concat

.join和.concat 经常混淆。.join(separator)以separator作为分隔符拼接数组元素，并返回字符串形式，如果没有提供separator，将使用默认的,。.concat会创建一个新数组，作为源数组的浅拷贝。

- .concat常用用法：array.concat(val, val2, val3, valn)
- .concat返回一个新数组
- array.concat()在没有参数的情况下，返回源数组的浅拷贝。

浅拷贝意味着新数组和原数组保持相同的对象引用，这通常是好事。例如：

```
var a = { foo: 'bar' }
var b = [1, 2, 3, a]
var c = b.concat()
console.log(b === c)
// <- false
b[3] === a && c[3] === a
// <- true
```

### 栈和队列的实现：.pop, .push, .shift和 .unshift

每个人都知道.push可以再数组末尾添加元素，但是你知道可以使用[].push(‘a’, ‘b’, ‘c’, ‘d’, ‘z’)一次性添加多个元素吗？

.pop 方法是.push 的反操作，它返回被删除的数组末尾元素。如果数组为空，将返回void 0 (undefined),使用.pop和.push可以创建LIFO (last in first out)栈。

```
function Stack () {
    this._stack = []
}
Stack.prototype.next = function () {
    return this._stack.pop()
}
Stack.prototype.add = function () {
    return this._stack.push.apply(this._stack, arguments)
}
stack = new Stack()
stack.add(1,2,3)
stack.next()
// <- 3
相反，可以使用.shift和 .unshift创建FIFO (first in first out)队列。

function Queue () {
    this._queue = []
}
Queue.prototype.next = function () {
    return this._queue.shift()
}
Queue.prototype.add = function () {
    return this._queue.unshift.apply(this._queue, arguments)
}
queue = new Queue()
queue.add(1,2,3)
queue.next()
// <- 1
Using .shift (or .pop) is an easy way to loop through a set of array elements, while draining the array in the process.
list = [1,2,3,4,5,6,7,8,9,10]
while (item = list.shift()) {
    console.log(item)
}
list
// <- []
```

### 模型映射：.map

> .map为数组中的每个元素提供了一个回调方法，并返回有调用结果构成的新数组。回调函数只对已经指定值的数组索引执行；它不会对已删除的或未指定值的元素调用。

Array.prototype.map 和上面提到的.forEach、.some和 .every有相同的参数格式：.map(fn(value, index, array), thisArgument)

```
values = [void 0, null, false, '']
values[7] = void 0
result = values.map(function(value, index, array){
    console.log(value)
    return value
})
// <- [undefined, null, false, '', undefined × 3, undefined]
```

undefined × 3很好地解释了.map不会对已删除的或未指定值的元素调用，但仍然会被包含在结果数组中。.map在创建或改变数组时非常有用，看下面的示例：

```
// casting
[1, '2', '30', '9'].map(function (value) {
    return parseInt(value, 10)
})
// 1, 2, 30, 9
[97, 119, 101, 115, 111, 109, 101].map(String.fromCharCode).join('')
// <- 'awesome'
// a commonly used pattern is mapping to new objects
items.map(function (item) {
    return {
        id: item.id,
        name: computeName(item)
    }
})
```

### 查询：.filter

> filter对每个数组元素执行一次回调函数，并返回一个由回调函数返回true的元素组成的新数组。回调函数只会对已经指定值的数组项调用。

通常用法：.filter(fn(value, index, array), thisArgument),跟C#中的LINQ表达式和SQL中的where语句类似，.filter只返回在回调函数中返回true值的元素。

```
[void 0, null, false, '', 1].filter(function (value) {
    return value
})
// <- [1]
[void 0, null, false, '', 1].filter(function (value) {
    return !value
})
// <- [void 0, null, false, '']
```

### 排序：.sort(compareFunction)

> 如果没有提供compareFunction，元素会被转换成字符串并按照字典排序。例如，”80″排在”9″之前，而不是在其后。

跟大多数排序函数类似，Array.prototype.sort(fn(a,b))需要一个包含两个测试参数的回调函数,其返回值如下：

- a在b之前则返回值小于0
- a和b相等则返回值是0
- a在b之后则返回值小于0

```
[9,80,3,10,5,6].sort()
// <- [10, 3, 5, 6, 80, 9]
[9,80,3,10,5,6].sort(function (a, b) {
    return a - b
})
// <- [3, 5, 6, 9, 10, 80]
```

### 计算：.reduce和.reduceRight

这两个函数比较难理解，.reduce会从左往右遍历数组，而.reduceRight则从右往左遍历数组，二者典型用法：.reduce(callback(previousValue,currentValue, index, array), initialValue)。

previousValue 是最后一次调用回调函数的返回值，initialValue则是其初始值，currentValue是当前元素值，index是当前元素索引，array是调用.reduce的数组。

一个典型的用例，使用.reduce的求和函数。

```
Array.prototype.sum = function () {
    return this.reduce(function (partial, value) {
        return partial + value
    }, 0)
};
[3,4,5,6,10].sum()
// <- 28
```

如果想把数组拼接成一个字符串，可以用.join实现。然而，若数组值是对象，.join就不会按照我们的期望返回值了，除非对象有合理的valueOf或toString方法，在这种情况下，可以用.reduce实现：

```
function concat (input) {
    return input.reduce(function (partial, value) {
        if (partial) {
            partial += ', '
        }
        return partial + value
    }, '')
}
concat([
    { name: 'George' },
    { name: 'Sam' },
    { name: 'Pear' }
])
// <- 'George, Sam, Pear'
```

### 复制：.slice

和.concat类似，调用没有参数的.slice()方法会返回源数组的一个浅拷贝。.slice有两个参数：一个是开始位置和一个结束位置。
Array.prototype.slice 能被用来将类数组对象转换为真正的数组。

```
Array.prototype.slice.call({ 0: 'a', 1: 'b', length: 2 })
// <- ['a', 'b']
这对.concat不适用，因为它会用数组包裹类数组对象。

Array.prototype.concat.call({ 0: 'a', 1: 'b', length: 2 })
// <- [{ 0: 'a', 1: 'b', length: 2 }]
```

此外，.slice的另一个通常用法是从一个参数列表中删除一些元素，这可以将类数组对象转换为真正的数组。

```
function format (text, bold) {
    if (bold) {
        text = '<b>' + text + '</b>'
    }
    var values = Array.prototype.slice.call(arguments, 2)
    values.forEach(function (value) {
        text = text.replace('%s', value)
    })
    return text
}
format('some%sthing%s %s', true, 'some', 'other', 'things')
```

### 强大的.splice

.splice 是我最喜欢的原生数组函数，只需要调用一次，就允许你删除元素、插入新的元素，并能同时进行删除、插入操作。需要注意的是，不同于`.concat和.slice,这个函数会改变源数组。

```
var source = [1,2,3,8,8,8,8,8,9,10,11,12,13]
var spliced = source.splice(3, 4, 4, 5, 6, 7)
console.log(source)
// <- [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12 ,13]
spliced
// <- [8, 8, 8, 8]
```

正如你看到的，.splice会返回删除的元素。如果你想遍历已经删除的数组时，这会非常方便。

```
var source = [1,2,3,8,8,8,8,8,9,10,11,12,13]
var spliced = source.splice(9)
spliced.forEach(function (value) {
    console.log('removed', value)
})
// <- removed 10
// <- removed 11
// <- removed 12
// <- removed 13
console.log(source)
// <- [1, 2, 3, 8, 8, 8, 8, 8, 9]
```

### 查找：.indexOf

利用.indexOf 可以在数组中查找一个元素的位置，没有匹配元素则返回-1。我经常使用.indexOf的情况是当我有比较时，例如：a === ‘a’ || a === ‘b’ || a === ‘c’，或者只有两个比较，此时，可以使用.indexOf：['a', 'b', 'c'].indexOf(a) !== -1。

注意，如果提供的引用相同，.indexOf也能查找对象。第二个可选参数用于指定开始查找的位置。

```
var a = { foo: 'bar' }
var b = [a, 2]
console.log(b.indexOf(1))
// <- -1
console.log(b.indexOf({ foo: 'bar' }))
// <- -1
console.log(b.indexOf(a))
// <- 0
console.log(b.indexOf(a, 1))
// <- -1
b.indexOf(2, 1)
// <- 1
```

如果你想从后向前搜索，可以使用.lastIndexOf。

### 操作符：in

在面试中新手容易犯的错误是混淆.indexOf和in操作符:

```
var a = [1, 2, 5]
1 in a
// <- true, but because of the 2!
5 in a
// <- false
```

问题是in操作符是检索对象的键而非值。当然，这在性能上比.indexOf快得多。

```
var a = [3, 7, 6]
1 in a === !!a[1]
// <- true
```

### 走近.reverse

该方法将数组中的元素倒置。

```
var a = [1, 1, 7, 8]
a.reverse()
// [8, 7, 1, 1]
```

.reverse 会修改数组本身。

转载自：[http://www.codeceo.com](http://www.codeceo.com/article/fun-with-javascript-native-array-functions.html)