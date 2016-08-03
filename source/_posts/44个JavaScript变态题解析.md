---
title: 59条令人捧腹但真实的程序员编程语录
date: 2016-07-12 10:18:37
tags: [javascript]
---

### 第1题

```
["1", "2", "3"].map(parseInt)
```

知识点:

- [Array/map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
- [Number/parseInt](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/parseInt)
- [JavaScript parseInt](http://www.w3school.com.cn/jsref/jsref_parseInt.asp)

首先, map接受两个参数, 一个回调函数 callback, 一个回调函数的this值

其中回调函数接受三个参数 currentValue, index, arrary;

而题目中, map只传入了回调函数–parseInt.

其次, parseInt 只接受两个两个参数 string, radix(基数).

> 可选。表示要解析的数字的基数。该值介于 2 ~ 36 之间。
>
> 如果省略该参数或其值为 0，则数字将以 10 为基础来解析。如果它以 “0x” 或 “0X” 开头，将以 16 为基数。
>
> 如果该参数小于 2 或者大于 36，则 parseInt() 将返回 NaN。

所以本题即问

```
parseInt('1', 0);
parseInt('2', 1);
parseInt('3', 2);
```

首先后两者参数不合法.

所以答案是 `[1, NaN, NaN]`

### 第2题

```
[typeof null, null instanceof Object]
```

两个知识点:

- [Operators/typeof](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof)
- [Operators/instanceof](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof)
- [Operators/instanceof(中)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/instanceof)

typeof 返回一个表示类型的字符串.

instanceof 运算符用来检测 constructor.prototype 是否存在于参数 object 的原型链上.

这个题可以直接看链接… 因为 `typeof null === 'object'` 自语言之初就是这样….

typeof 的结果请看下表:

```
type         result
Undefined   "undefined"
Null        "object"
Boolean     "boolean"
Number      "number"
String      "string"
Symbol      "symbol"
Host object Implementation-dependent
Function    "function"
Object      "object"
```

所以答案 `[object, false]`

### 第3题

```
[ [3,2,1].reduce(Math.pow), [].reduce(Math.pow) ]
```

知识点:

- [Array/Reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

`arr.reduce(callback[, initialValue])`

reduce接受两个参数, 一个回调, 一个初始值.

回调函数接受四个参数 `previousValue, currentValue, currentIndex, array`

需要注意的是 `If the array is empty and no initialValue was provided, TypeError would be thrown.`

所以第二个表达式会报异常. 第一个表达式等价于 `Math.pow(3, 2) => 9; Math.pow(9, 1) =>9`

答案 `an error`

### 第4题

```
var val = 'smtg';
console.log('Value is ' + (val === 'smtg') ? 'Something' : 'Nothing');
```

两个知识点:

- [Operators/Operator_Precedence](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)
- [Operators/Conditional_Operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)

简而言之 `+` 的优先级 大于 `?`

所以原题等价于 `'Value is true' ? 'Somthing' : 'Nonthing'` 而不是 `'Value is' + (true ? 'Something' : 'Nonthing')`

答案 `'Something'`

### 第5题

```
var name = 'World!';
(function () {
    if (typeof name === 'undefined') {
        var name = 'Jack';
        console.log('Goodbye ' + name);
    } else {
        console.log('Hello ' + name);
    }
})();
```

这个相对简单, 一个知识点:

- [Hoisting](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting)

在 JavaScript中， functions 和 variables 会被提升。变量提升是JavaScript将声明移至作用域 scope (全局域或者当前函数作用域) 顶部的行为。

这个题目相当于

```
var name = 'World!';
(function () {
    var name;
    if (typeof name === 'undefined') {
        name = 'Jack';
        console.log('Goodbye ' + name);
    } else {
        console.log('Hello ' + name);
    }
})();
```

所以答案是 `'Goodbye Jack'`

### 第6题

```
var END = Math.pow(2, 53);
var START = END - 100;
var count = 0;
for (var i = START; i <= END; i++) {
    count++;
}
console.log(count);
```

一个知识点:

- [Infinity](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Infinity)

~~在 JS 里, Math.pow(2, 53) == 9007199254740992 是可以表示的最大值. 最大值加一还是最大值. 所以循环不会停.~~

补充: [@jelly7723](https://github.com/jelly7723)

> js中可以表示的最大整数不是2的53次方，而是1.7976931348623157e+308。
> 2的53次方不是js能表示的最大整数而应该是能正确计算且不失精度的最大整数，可以参见js权威指南。
> 9007199254740992 +1还是 9007199254740992 ，这就是因为精度问题，如果 9007199254740992 +11或者 9007199254740992 +111的话，值是会发生改变的，只是这时候计算的结果不是正确的值，就是因为精度丢失的问题。

### 第7题

```
var ary = [0,1,2];
ary[10] = 10;
ary.filter(function(x) { return x === undefined;});
```

答案是 `[]`

看一篇文章理解稀疏数组

- [译 JavaScript中的稀疏数组与密集数组](http://www.cnblogs.com/ziyunfei/archive/2012/09/16/2687165.html)
- [Array/filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)

我们来看一下 Array.prototype.filter 的 polyfill:

```
if (!Array.prototype.filter) {
  Array.prototype.filter = function(fun/*, thisArg*/) {
    'use strict';

    if (this === void 0 || this === null) {
      throw new TypeError();
    }

    var t = Object(this);
    var len = t.length >>> 0;
    if (typeof fun !== 'function') {
      throw new TypeError();
    }

    var res = [];
    var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
    for (var i = 0; i < len; i++) {
      if (i in t) { // 注意这里!!!
        var val = t[i];
        if (fun.call(thisArg, val, i, t)) {
          res.push(val);
        }
      }
    }

    return res;
  };
}
```

我们看到在迭代这个数组的时候, 首先检查了这个索引值是不是数组的一个属性, 那么我们测试一下.

```
0 in ary; => true
3 in ary; => false
10 in ary; => true
```

也就是说 从 3 – 9 都是没有初始化的’坑’!, 这些索引并不存在与数组中. 在 array 的函数调用的时候是会跳过这些’坑’的.

### 第8题

```
var two   = 0.2
var one   = 0.1
var eight = 0.8
var six   = 0.6
[two - one == one, eight - six == two]
```

- [JavaScript的设计缺陷?浮点运算：0.1 + 0.2 != 0.3](http://ourjs.com/detail/54695381bc3f9b154e000046)

IEEE 754标准中的[浮点数](http://www.codeceo.com/article/float-number.html)并不能精确地表达小数

那什么时候精准, 什么时候不经准呢? 笔者也不知道…

答案 `[true, false]`

### 第9题

```
function showCase(value) {
    switch(value) {
    case 'A':
        console.log('Case A');
        break;
    case 'B':
        console.log('Case B');
        break;
    case undefined:
        console.log('undefined');
        break;
    default:
        console.log('Do not know!');
    }
}
showCase(new String('A'));
```

两个知识点:

- [Statements/switch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/switch)
- [String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)

switch 是严格比较, String 实例和 字符串不一样.

```
var s_prim = 'foo';
var s_obj = new String(s_prim);

console.log(typeof s_prim); // "string"
console.log(typeof s_obj);  // "object"
console.log(s_prim === s_obj); // false
```

答案是 `'Do not know!'`

### 第10题

```
function showCase2(value) {
    switch(value) {
    case 'A':
        console.log('Case A');
        break;
    case 'B':
        console.log('Case B');
        break;
    case undefined:
        console.log('undefined');
        break;
    default:
        console.log('Do not know!');
    }
}
showCase2(String('A'));
```

解释:

`String(x) does not create an object but does return a string, i.e. typeof String(1) === "string"`

还是刚才的知识点, 只不过 String 不仅是个构造函数 直接调用返回一个字符串哦.

答案 `'Case A'`

### 第11题

```
function isOdd(num) {
    return num % 2 == 1;
}
function isEven(num) {
    return num % 2 == 0;
}
function isSane(num) {
    return isEven(num) || isOdd(num);
}
var values = [7, 4, '13', -9, Infinity];
values.map(isSane);
```

一个知识点

- [Arithmetic_Operators#Remainder](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Remainder)

此题等价于

```
7 % 2 => 1
4 % 2 => 0
'13' % 2 => 1
-9 % % 2 => -1
Infinity % 2 => NaN
```

需要注意的是 余数的正负号随第一个操作数.

答案 `[true, true, true, false, false]`

### 第12题

```
parseInt(3, 8)
parseInt(3, 2)
parseInt(3, 0)
```

第一个题讲过了, 答案 `3, NaN, 3`

### 第13题

```
Array.isArray( Array.prototype )
```

一个知识点:

- [Array/prototype](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/prototype)

一个鲜为人知的实事: `Array.prototype => []`;

答案: `true`

### 第14题

```
var a = [0];
if ([0]) {
  console.log(a == true);
} else {
  console.log("wut");
}
```

- [JavaScript-Equality-Table](https://dorey.github.io/JavaScript-Equality-Table/)

答案: `false`

### 第15题

```
[]==[]
```

`==` 是万恶之源, 看上图

答案是 `false`

### 第16题

```
'5' + 3
'5' - 3
```

两个知识点:

- [Arithmetic_Operators#Addition](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Addition)
- [Arithmetic_Operators#Subtraction](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Subtraction)

`+` 用来表示两个数的和或者字符串拼接, `-`表示两数之差.

请看例子, 体会区别:

```
> '5' + 3
'53'
> 5 + '3'
'53'
> 5 - '3'
2
> '5' - 3
2
> '5' - '3'
2
```

也就是说 `-` 会尽可能的将两个操作数变成数字, 而 `+` 如果两边不都是数字, 那么就是字符串拼接.

答案是 `'53', 2`

### 第17题

```
1 + - + + + - + 1
```

这里应该是(倒着看)

```
1 + (a)  => 2
a = - (b) => 1
b = + (c) => -1
c = + (d) => -1
d = + (e) => -1
e = + (f) => -1
f = - (g) => -1
g = + 1   => 1
```

所以答案 `2`

### 第18题

```
var ary = Array(3);
ary[0]=2
ary.map(function(elem) { return '1'; });
```

稀疏数组. 同第7题.

题目中的数组其实是一个长度为3, 但是没有内容的数组, array 上的操作会跳过这些未初始化的’坑’.

所以答案是 `["1", undefined × 2]`

这里贴上 Array.prototype.map 的 polyfill.

```
Array.prototype.map = function(callback, thisArg) {

        var T, A, k;

        if (this == null) {
            throw new TypeError(' this is null or not defined');
        }

        var O = Object(this);
        var len = O.length >>> 0;
        if (typeof callback !== 'function') {
            throw new TypeError(callback + ' is not a function');
        }
        if (arguments.length > 1) {
            T = thisArg;
        }
        A = new Array(len);
        k = 0;
        while (k < len) {
            var kValue, mappedValue;
            if (k in O) {
                kValue = O[k];
                mappedValue = callback.call(T, kValue, k, O);
                A[k] = mappedValue;
            }
            k++;
        }
        return A;
    };
```

### 第19题

```
function sidEffecting(ary) {
  ary[0] = ary[2];
}
function bar(a,b,c) {
  c = 10
  sidEffecting(arguments);
  return a + b + c;
}
bar(1,1,1)
```

这是一个大坑, 尤其是涉及到 ES6语法的时候

知识点:

- [Functions/arguments](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)

首先 `The arguments object is an Array-like object corresponding to the arguments passed to a function.`

也就是说 `arguments` 是一个 `object`, c 就是 arguments[2], 所以对于 c 的修改就是对 arguments[2] 的修改.

所以答案是 `21`.

然而!!!!!!

当函数参数涉及到 `any rest parameters, any default parameters or any destructured parameters` 的时候, 这个 arguments 就不在是一个 `mapped arguments object` 了…..

请看:

```
function sidEffecting(ary) {
  ary[0] = ary[2];
}
function bar(a,b,c=3) {
  c = 10
  sidEffecting(arguments);
  return a + b + c;
}
bar(1,1,1)
```

答案是 `12` !!!!

请读者细细体会!!

### 第20题

```
var a = 111111111111111110000,
    b = 1111;
a + b;
```

答案还是 `111111111111111110000`. 解释是 `Lack of precision for numbers in JavaScript affects both small and big numbers.` 但是笔者不是很明白……………. 请读者赐教!

### 第21题

```
var x = [].reverse;
x();
```

这个题有意思!

知识点:

- [Array/reverse](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse)

`The reverse method transposes the elements of the calling array object in place, mutating the array, and returning a reference to the array.`

也就是说 最后会返回这个调用者(this), 可是 x 执行的时候是上下文是全局. 那么最后返回的是 `window`.

答案是 `window`

### 第22题

```
Number.MIN_VALUE > 0
```

`true`

### 第23题

```
[1 < 2 < 3, 3 < 2 < 1]
```

这个题也还可以.

这个题会让人误以为是 `2 > 1 && 2 < 3` 其实不是的.

这个题等价于

```
 1 < 2 => true;
 true < 3 =>  1 < 3 => true;
 3 < 2 => false;
 false < 1 => 0 < 1 => true;
```

答案是 `[true, true]`

### 第24题

```
// the most classic wtf
2 == [[[2]]]
```

这个题我是猜的. 我猜的 `true`, 至于为什么…..

`both objects get converted to strings and in both cases the resulting string is "2"` 我不能信服…

### 第25题

```
3.toString()
3..toString()
3...toString()
```

这个题也挺逗, 我做对了![:)](http://www.codeceo.com/wp-content/themes/d-simple/img/smilies/icon_smile.gif)答案是 `error, '3', error`

你如果换一个写法就更费解了

```
var a = 3;
a.toString()
```

这个答案就是 `'3'`;

为啥呢?

因为在 js 中 `1.1`, `1.`, `.1` 都是合法的数字. 那么在解析 `3.toString` 的时候这个 `.` 到底是属于这个数字还是函数调用呢? 只能是数字, 因为`3.`合法啊!

### 第26题

```
(function(){
  var x = y = 1;
})();
console.log(y);
console.log(x);
```

答案是 `1, error`

y 被赋值到全局. x 是局部变量. 所以打印 x 的时候会报 `ReferenceError`

### 第27题

```
var a = /123/,
    b = /123/;
a == b
a === b
```

即使正则的字面量一致, 他们也不相等.

答案 `false, false`

### 第28题

```
var a = [1, 2, 3],
    b = [1, 2, 3],
    c = [1, 2, 4]
a ==  b
a === b
a >   c
a <   c
```

字面量相等的数组也不相等.

数组在比较大小的时候按照字典序比较

答案 `false, false, false, true`

### 第29题

```
var a = {}, b = Object.prototype;
[a.prototype === b, Object.getPrototypeOf(a) === b]
```

知识点:

- [Object/getPrototypeOf](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf)

只有 Function 拥有一个 prototype 的属性. 所以 `a.prototype` 为 `undefined`.

而 `Object.getPrototypeOf(obj)` 返回一个具体对象的原型(该对象的内部`[[prototype]]`值)

答案 `false, true`

### 第30题

```
function f() {}
var a = f.prototype, b = Object.getPrototypeOf(f);
a === b
```

f.prototype is the object that will become the parent of any objects created with new f while Object.getPrototypeOf returns the parent in the inheritance hierarchy.

f.prototype 是使用使用 new 创建的 f 实例的原型. 而 Object.getPrototypeOf 是 f 函数的原型.

请看:

```
a === Object.getPrototypeOf(new f()) // true
b === Function.prototype // true
```

答案 `false`

### 第31题

```
function foo() { }
var oldName = foo.name;
foo.name = "bar";
[oldName, foo.name]
```

答案 `['foo', 'foo']`

知识点:

- [Function/name](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/name)

因为函数的名字不可变.

### 第32题

```
"1 2 3".replace(/\d/g, parseInt)
```

知识点:

- [String/replace#Specifying_a_function_as_a_parameter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace#Specifying_a_function_as_a_parameter)

`str.replace(regexp|substr, newSubStr|function)`

如果replace函数传入的第二个参数是函数, 那么这个函数将接受如下参数

- match 首先是匹配的字符串
- p1, p2 …. 然后是正则的分组
- offset match 匹配的index
- string 整个字符串

由于题目中的正则没有分组, 所以等价于问

```
parseInt('1', 0)
parseInt('2', 2)
parseInt('3', 4)
```

答案: `1, NaN, 3`

### 第33题

```
function f() {}
var parent = Object.getPrototypeOf(f);
f.name // ?
parent.name // ?
typeof eval(f.name) // ?
typeof eval(parent.name) //  ?
```

先说以下答案 `'f', 'Empty', 'function', error` 这个答案并不重要…..

这里第一小问和第三小问很简单不解释了.

第二小问笔者在自己的浏览器测试的时候是 `''`, 第四问是 `'undefined'`

所以应该是平台相关的. 这里明白 `parent === Function.prototype` 就好了.

### 第34题

```
var lowerCaseOnly =  /^[a-z]+$/;
[lowerCaseOnly.test(null), lowerCaseOnly.test()]
```

知识点:

- [RegExp/test](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test)

这里 test 函数会将参数转为字符串. `'nul'`, `'undefined'` 自然都是全小写了

答案: `true, true`

### 第35题

```
[,,,].join(", ")
```

`[,,,] => [undefined × 3]`

因为javascript 在定义数组的时候允许最后一个元素后跟一个`,`, 所以这是个长度为三的稀疏数组(这是长度为三, 并没有 0, 1, 2三个属性哦)

答案: `", , "`

### 第36题

```
var a = {class: "Animal", name: 'Fido'};
a.class
```

这个题比较流氓.. 因为是浏览器相关, `class`是个保留字(现在是个关键字了)

所以答案不重要, 重要的是自己在取属性名称的时候尽量避免保留字. 如果使用的话请加引号 `a['class']`

### 第37题

```
var a = new Date("epoch")
```

知识点:

- [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)
- [Date/parse](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/parse)

简单来说, 如果调用 Date 的构造函数传入一个字符串的话需要符合规范, 即满足 Date.parse 的条件.

另外需要注意的是 如果格式错误 构造函数返回的仍是一个Date 的实例 `Invalid Date`.

答案 `Invalid Date`

### 第38题

```
var a = Function.length,
    b = new Function().length
a === b
```

我们知道一个function(Function 的实例)的 `length` 属性就是函数签名的参数个数, 所以 b.length == 0.

另外 Function.length 定义为1……

所以不相等…….答案 `false`

### 第39题

```
var a = Date(0);
var b = new Date(0);
var c = new Date();
[a === b, b === c, a === c]
```

还是关于Date 的题, 需要注意的是

- 如果不传参数等价于当前时间.
- 如果是函数调用 返回一个字符串.

答案 `false, false, false`

### 第40题

```
var min = Math.min(), max = Math.max()
min < max
```

知识点:

- [Math/min](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/min)
- [Math/max](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max)

有趣的是, Math.min 不传参数返回 `Infinity`, Math.max 不传参数返回 `-Infinity`

答案: `false`

### 第41题

```
function captureOne(re, str) {
  var match = re.exec(str);
  return match && match[1];
}
var numRe  = /num=(\d+)/ig,
    wordRe = /word=(\w+)/i,
    a1 = captureOne(numRe,  "num=1"),
    a2 = captureOne(wordRe, "word=1"),
    a3 = captureOne(numRe,  "NUM=2"),
    a4 = captureOne(wordRe,  "WORD=2");
[a1 === a2, a3 === a4]
```

知识点:

- [RegExp/exec](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec)

通俗的讲

因为第一个正则有一个 g 选项 它会‘记忆’他所匹配的内容, 等匹配后他会从上次匹配的索引继续, 而第二个正则不会

举个例子

```
var myRe = /ab*/g;
var str = 'abbcdefabh';
var myArray;
while ((myArray = myRe.exec(str)) !== null) {
  var msg = 'Found ' + myArray[0] + '. ';
  msg += 'Next match starts at ' + myRe.lastIndex;
  console.log(msg);
}
// Found abb. Next match starts at 3
// Found ab. Next match starts at 9
```

所以 a1 = ’1′; a2 = ’1′; a3 = null; a4 = ’2′

答案 `[true, false]`

### 第42题

```
var a = new Date("2014-03-19"),
    b = new Date(2014, 03, 19);
[a.getDay() === b.getDay(), a.getMonth() === b.getMonth()]
```

这个….

> JavaScript inherits 40 years old design from C: days are 1-indexed in C’s struct tm, but months are 0 indexed. In addition to that, getDay returns the 0-indexed day of the week, to get the 1-indexed day of the month you have to use getDate, which doesn’t return a Date object.

```
a.getDay()
3
b.getDay()
6
a.getMonth()
2
b.getMonth()
3
```

都是套路!

答案 `[false, false]`

### 第43题

```
if ('http://giftwrapped.com/picture.jpg'.match('.gif')) {
  'a gif file'
} else {
  'not a gif file'
}
```

知识点:

- [String/match](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match)

String.prototype.match 接受一个正则, 如果不是, 按照 `new RegExp(obj)` 转化. 所以 `.` 并不会转义
那么 `/gif` 就匹配了 /.gif/

答案: `'a gif file'`

### 第44题

```
function foo(a) {
    var a;
    return a;
}
function bar(a) {
    var a = 'bye';
    return a;
}
[foo('hello'), bar('hello')]
```

在两个函数里, a作为参数其实已经声明了, 所以 `var a; var a = 'bye'` 其实就是 `a; a ='bye'`

所以答案 `'hello', 'bye'`

转载自：[http://www.codeceo.com](http://www.codeceo.com/article/44-javascript-crazy-question.html)
英文原文：[javascript-puzzlers](http://javascript-puzzlers.herokuapp.com/)