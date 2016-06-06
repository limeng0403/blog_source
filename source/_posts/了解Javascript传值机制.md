---
title: 了解Javascript传值机制
tags: [javascript]
date: 2015/02/13
---

理论上，JavaScript通过值传递。它既不是值传递也不是引用传递，具体取决于它的真实场景。要理解传值机制，看一下下面两个实例代码和解释。

### 实例 1

```
var me = {                  // 1
    'partOf' : 'A Team'
}; 

function myTeam(me) {       // 2

    me = {                  // 3
        'belongsTo' : 'A Group'
    }; 
}   

myTeam(me);     
console.log(me);            // 4  : {'partOf' : 'A Team'}
```

在上面的实例里myTeam被调用的时候，JavaScript 传递me对象的引用值，因为它是一个对象。而且调用本身建立了同一个对象的两个独立的引用，（虽然在这里的的命名都是相同的，比如me, 这有些无调行，而且给我们一个这是单个引用的印象）因此，引用变量本身是独立的。
当我们在#3定义了一个新的对象，我们完全改变了myTeam函数内的引用值，这对此函数作用域外的原始对象是没有任何影响的，外作用域的引用仍保留在原始对象上，因此从#4输出去了。

### 实例 2

```
var me = {                  // 1
    'partOf' : 'A Team'
}; 

function myGroup(me) {      // 2
    me.partOf = 'A Group';  // 3
} 

myGroup(me);
console.log(me);            // 4  : {'partOf' : 'A Group'}
```

当myGroup调用时，我们将对象me传给函数。但是与实例1的情况不同，我们没有指派me变量到任何新对象，有效的说明了myGroup函数作用域内的对象引用值依旧是原始对象的引用值，而且我们在作用域内修改对象的参数值同样有效的修改了原始对象的参数。因此你得到了#7的输出结果。
所以后面的例子是否说明javascript是引用传递呢？不，并没有。请记住，如果是对象的话，JavaScript将引用按值传递。这种混乱往往发生在我们没有完全理解什么通过引用传递的情况下。这就是确切的原因，有些人更愿意称它为call-by-sharing。

[转载自js-by-examples](https://github.com/bmkmanoj/js-by-examples/blob/master/examples/js_pass_by_value_or_reference.md)
