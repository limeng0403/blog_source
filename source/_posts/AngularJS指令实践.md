---
title: AngularJS 指令实践
tags: [angularjs]
date: 2016-8-3
---

指令（Directives）是所有AngularJS应用最重要的部分。尽管AngularJS已经提供了非常丰富的指令，但还是经常需要创建应用特定的指令。这篇教程会为你讲述如何自定义指令，以及介绍如何在实际项目中使用。在这篇文章的最后，我会指导你如何使用Angular指令来创建一个简单的记事本应用。

### 概述

一个指令用来引入新的HTML语法。指令是DOM元素上的标记，使元素拥有特定的行为。举例来说，静态的HTML不知道如何来创建和展现一个日期选择器控件。让HTML能识别这个语法，我们需要使用指令。指令通过某种方法来创建一个能够支持日期选择的元素。我们会循序渐进地介绍这是如何实现的。 如果你写过AngularJS的应用，那么你一定已经使用过指令，不管你有没有意识到。你肯定已经用过简单的指令，比如 ng-mode, ng-repeat, ng-show等。这些指令都赋予DOM元素特定的行为。例如，ng-repeat 重复特定的元素，ng-show 有条件地显示一个元素。如果你想让一个元素支持拖拽，你也需要创建一个指令来实现它。指令背后基本的想法很简单。它通过对元素绑定事件监听或者改变DOM而使HTML拥有真实的交互性。

### jQuery视角

想象一下使用jQuery如何创建一个日期选择器。首先，我们在HTML中添加一个普通的输入框，然后通过jQuery调用 $(element).dataPicker() 来将它转变成一个日期选择器。但是，仔细想一下。当一个设计人员过来检查HTML标记的时候，他/她能否立刻猜到这个字段实际上表示的内容？这只是一个简单的输入框，或者一个日期选择器？你需要查看jQuery代码来确定这些。而Angular的方法是使用一个指令来扩展HTML。所以，一个日期选择器的指令可以是下面的形式：

```html
<input type="text" />
```

创建自定义指令：这种创建UI组建的方式更加直接和清晰。你可以轻易地通过查看元素就明白这到底是什么。

一个Angular指令可以有以下的四种表现形式：

1. 一个新的HTML元素（<data-picker></data-picker>）

2. 元素的属性（<input type=”text” data-picker/>）

3. CSS class（<input type=”text” class=”data-picker”/>）

4. 注释（`<!–directive:data-picker –>`）

当然，我们可以控制我们的指令在HTML中的表现形式。下面我们来看一下AngularJS中的一个典型的指令的写法。指令注册的方式与 controller 一样，但是它返回的是一个拥有指令配置属性的简单对象(指令定义对象) 。下面的代码是一个简单的 [Hello World](http://www.codeceo.com/article/hello-world-30-years.html) 指令。

```javascript
var app = angular.module('myapp', []);

app.directive('helloWorld', function() {

  return {

      restrict: 'AE',

      replace: 'true',

      template: '<h3>Hello World!!</h3>'

  };

});
```

在上面的代码中，app.directive()方法在模块中注册了一个新的指令。这个方法的第一个参数是这个指令的名字。第二个参数是一个返回指令定义对象的函数。如果你的指令依赖于其他的对象或者服务，比如 $rootScope, $http, 或者$compile，他们可以在这个时间被注入。这个指令在HTML中以一个元素使用，如下：

```html
<hello-world/>

//OR

<hello:world/>
```

或者，以一个属性的方式使用：

```html
<div hello-world></div>

//OR

<div hello:world/>
```

如果你想要符合HTML5的规范，你可以在元素前面添加 x- 或者 data-的前缀。所以下面的标记也会匹配 helloWorld 指令：

```html
<div data-hello-world></div>

//OR

<div x-hello-world></div>
```

restrict – 这个属性用来指定指令在HTML中如何使用（还记得之前说的，指令的四种表示方式吗）。在上面的例子中，我们使用了 ‘AE’。所以这个指令可以被当作新的HTML元素或者属性来使用。如果要允许指令被当作class来使用，我们将 restrict 设置成 ‘AEC’。

**注意：** 在匹配指令的时候，Angular会在元素或者属性的名字中剔除 x- 或者 data- 前缀。 然后将 – 或者 : 连接的字符串转换成驼峰(camelCase)表现形式，然后再与注册过的指令进行匹配。这是为什么，我们在HTML中以 hello-world 的方式使用 helloWorld 指令。其实，这跟HTML对标签和属性不区分大小写有关。 尽管上面的指令仅仅实现了静态文字的显示，但是这里还是有一些有趣的点值得我们去挖掘。我们在指令定义过程中使用了三个属性来配置指令。我们来一一介绍他们的作用。

- template – 这个属性规定了指令被Angular编译和链接（link）后生成的HTML标记。这个属性值不一定要是简单的字符串。template 可以非常复杂，而且经常包含其他的指令，以及表达式({{ }})等。更多的情况下你可能会见到 templateUrl， 而不是 template。所以，理想情况下，你应该将模板放到一个特定的HTML文件中，然后将 templateUrl 属性指向它。
- replace – 这个属性指明生成的HTML内容是否会替换掉定义此指令的HTML元素。在我们的例子中，我们用 <hello-world></hello-world>的方式使用我们的指令，并且将 replace 设置成 true。所以，在指令被编译之后，生成的模板内容替换掉了 <hello-world></hello-world>。最终的输出是 <h3>Hello World!!</h3>。如果你将 replace 设置成 false，也就是默认值，那么生成的模板会被插入到定义指令的元素中。

打开这个 [plunker](http://plnkr.co/edit/GKI339z2VDdZTOE2bGFP)，在”Hello World!!”右键检查元素内容，来更形象地明白这些。

### Link函数和Scope

指令生成出的模板其实没有太多意义，除非它在特定的scope下编译。默认情况下，指令并不会创建新的子scope。更多的，它使用父scope。也就是说，如果指令存在于一个controller下，它就会使用这个controller的scope。 如何运用scope，我们要用到一个叫做 link 的函数。它由指令定义对象中的link属性配置。让我们来改变一下我们的 helloWorld 指令，当用户在一个输入框中输入一种颜色的名称时，Hello World 文字的背景色自动发生变化。同时，当用户在 Hello World 文字上点击时，背景色变回白色。 相应的HTML标记如下：

```html
<body ng-controller="MainCtrl">

  <input type="text" ng-model="color" placeholder="Enter a color" />

  <hello-world/>

</body>
```

JavaScript修改后的 helloWorld 指令如下：

```javascript
app.directive('helloWorld', function() {

  return {
    restrict: 'AE',
    replace: true,

    template: '<p style="background-color:{{color}}">Hello World',

    link: function(scope, elem, attrs) {

      elem.bind('click', function() {

        elem.css('background-color', 'white');

        scope.$apply(function() {

          scope.color = "white";

        });

      });

      elem.bind('mouseover', function() {

        elem.css('cursor', 'pointer');

      });
    }
  };
});
```

scope – 指令的scope。在我们的例子中，指令的scope就是父controller的scope。我们注意到指令定义中的 link 函数。 它有三个参数：

- elem – 指令的jQLite(jQuery的子集)包装DOM元素。如果你在引入AngularJS之前引入了jQuery，那么这个元素就是jQuery元素，而不是jQLite元素。由于这个元素已经被jQuery/jQLite包装了，所以我们就在进行DOM操作的时候就不需要再使用 $()来进行包装。
- attr – 一个包含了指令所在元素的属性的标准化的参数对象。举个例子，你给一个HTML元素添加了一些属性：，那么可以在 link 函数中通过 attrs.someAttribute 来使用它。

link函数主要用来为DOM元素添加事件监听、监视模型属性变化、以及更新DOM。在上面的指令代码片段中，我们添加了两个事件， click，和 mouseover。click 处理函数用来重置 <p> 的背景色，而 mouseover 处理函数改变鼠标为 pointer。在模板中有一个表达式 {{color}}，当父scope中的 color 发生变化时，它用来改变 Hello World 文字的背景色。 这个 plunker 演示了这些概念。

### compile函数

compile 函数在 link 函数被执行之前用来做一些DOM改造。它接收下面的参数：

- tElement – 指令所在的元素
- attrs – 元素上赋予的参数的标准化列表

要注意的是 compile 函数不能访问 scope，并且必须返回一个 link 函数。但是如果没有设置 compile 函数，你可以正常地配置 link 函数，（有了compile，就不能用link，link函数由compile返回）。compile函数可以写成如下的形式：

```javascript
app.directive('test', function() {

  return {

    compile: function(tElem,attrs) {

      //do optional DOM transformation here

      return function(scope,elem,attrs) {

        //linking function here

      };
    }
  };
});
```

指令是如何被编译的大多数的情况下，你只需要使用 link 函数。这是因为大部分的指令只需要考虑注册事件监听、监视模型、以及更新DOM等，这些都可以在 link 函数中完成。 但是对于像 ng-repeat 之类的指令，需要克隆和重复 DOM 元素多次，在 link 函数执行之前由 compile 函数来完成。这就带来了一个问题，为什么我们需要两个分开的函数来完成生成过程，为什么不能只使用一个？要回答好这个问题，我们需要理解指令在Angular中是如何被编译的！

当应用引导启动的时候，Angular开始使用 $compile 服务遍历DOM元素。这个服务基于注册过的指令在标记文本中搜索指令。一旦所有的指令都被识别后，Angular执行他们的 compile 方法。如前面所讲的，compile 方法返回一个 link 函数，被添加到稍后执行的 link 函数列表中。这被称为编译阶段。如果一个指令需要被克隆很多次（比如 ng-repeat），compile函数只在编译阶段被执行一次，复制这些模板，但是link 函数会针对每个被复制的实例被执行。所以分开处理，让我们在性能上有一定的提高。这也说明了为什么在 compile 函数中不能访问到scope对象。 在编译阶段之后，就开始了链接（linking）阶段。在这个阶段，所有收集的 link 函数将被一一执行。指令创造出来的模板会在正确的scope下被解析和处理，然后返回具有事件响应的真实的DOM节点。

### 改变指令的Scope

默认情况下，指令获取它父节点的controller的scope。但这并不适用于所有情况。如果将父controller的scope暴露给指令，那么他们可以随意地修改 scope 的属性。在某些情况下，你的指令希望能够添加一些仅限内部使用的属性和方法。如果我们在父的scope中添加，会污染父scope。 其实我们还有两种选择：

- 一个子scope – 这个scope原型继承子父scope。
- 一个隔离的scope – 一个孤立存在不继承自父scope的scope。

这样的scope可以通过指令定义对象中 scope 属性来配置。下面的代码片段是一个例子：

```javascript
app.directive('helloWorld', function() {

  return {

    scope: true,  // use a child scope that inherits from parent

    restrict: 'AE',

    replace: 'true',

    template: '<h3>Hello World!!</h3>'

  };

});
```

上面的代码，让Angular给指令创建一个继承自父socpe的新的子scope。 另外一个选择，隔离的scope：

```javascript
app.directive('helloWorld', function() {

  return {

    scope: {},  // use a new isolated scope

    restrict: 'AE',

    replace: 'true',

    template: '<h3>Hello World!!</h3>'

  };

});
```

这个指令使用了一个隔离的scope。隔离的scope在我们想要创建可重用的指令的时候是非常有好处的。通过使用隔离的scope，我们能够保证我们的指令是自包含的，可以被很容易的插入到HTML应用中。 它内部不能访问父的scope，所保证了父scope不被污染。 在我们的 helloWorld 指令例子中，如果我们将 scope 设置成 {}，那么上面的代码将不会工作。 它会创建一个新的隔离的scope，那么相应的表达式 {{color}} 会指向到这个新的scope中，它的值将是 undefined. 使用隔离的scope并不意味着我们完全不能访问父scope的属性。

### 隔离scope和父scope之间的数据绑定

通常，隔离指令的scope会带来很多的便利，尤其是在你要操作多个scope模型的时候。但有时为了使代码能够正确工作，你也需要从指令内部访问父scope的属性。好消息是Angular给了你足够的灵活性让你能够有选择性的通过绑定的方式传入父scope的属性。让我们重温一下我们的 [helloWorld](http://plnkr.co/edit/14q6WxHyhWuVxEIqwww1?p=preview) 指令，它的背景色会随着用户在输入框中输入的颜色名称而变化。还记得当我们对这个指令使用隔离scope的之后，它不能工作了吗？现在，我们来让它恢复正常。

假设我们已经初始化完成app这个变量所指向的Angular模块。那么我们的 helloWorld 指令如下面代码所示：

```javascript
app.directive('helloWorld', function() {

  return {

    scope: {},

    restrict: 'AE',

    replace: true,

    template: '<p style="background-color:{{color}}">Hello World</p>',

    link: function(scope, elem, attrs) {

      elem.bind('click', function() {

        elem.css('background-color','white');

        scope.$apply(function() {

          scope.color = "white";

        });

      });

      elem.bind('mouseover', function() {

        elem.css('cursor', 'pointer');

      });
    }
  };
});
```

使用这个指令的HTML标签如下：

```html
<body ng-controller="MainCtrl">

  <input type="text" ng-model="color" placeholder="Enter a color"/>

  <hello-world/>

</body>
```

**选择一：**使用 @ 实现单向文本绑定上面的代码现在是不能工作的。因为我们用了一个隔离的scope，指令内部的 {{color}} 表达式被隔离在指令内部的scope中(不是父scope)。但是外面的输入框元素中的 ng-model 指令是指向父scope中的 color 属性的。所以，我们需要一种方式来绑定隔离scope和父scope中的这两个参数。在Angular中，这种数据绑定可以通过为指令所在的HTML元素添加属性和并指令定义对象中配置相应的 scope 属性来实现。让我们来细究一下建立数据绑定的几种方式。

在下面的指令定义中，我们指定了隔离scope中的属性 color 绑定到指令所在HTML元素上的参数 colorAttr。在HTML标记中，你可以看到 {{color}}表达式被指定给了 color-attr 参数。当表达式的值发生改变时，color-attr 参数也跟着改变。隔离scope中的 color 属性的值也相应地被改变。

```javascript
app.directive('helloWorld', function() {

  return {

    scope: {

      color: '@colorAttr'

    },

    ....

    // the rest of the configurations

  };

});
```

更新后的HTML标记代码如下：

```html
<body ng-controller="MainCtrl">

  <input type="text" ng-model="color" placeholder="Enter a color"/>

  <hello-world color-attr="{{color}}"/>

</body>
```

注意点：我们称这种方式为单项绑定，是因为在这种方式下，你只能将字符串(使用表达式{{}})传递给参数。当父scope的属性变化时，你的隔离scope模型中的属性值跟着变化。你甚至可以在指令内部监控这个scope属性的变化，并且触发一些任务。然而，反向的传递并不工作。你不能通过对隔离scope属性的操作来改变父scope的值。

当隔离scope属性和指令元素参数的名字一样是，你可以更简单的方式设置scope绑定：

```javascript
app.directive('helloWorld', function() {

  return {

    scope: {

      color: '@'

    },

    ....

    // the rest of the configurations

  };

});
```

相应使用指令的HTML代码如下：

```html
<hello-world color="{{color}}"/>
```

**选择二：**使用 = 实现双向绑定

让我们将指令的定义改变成下面的样子：

```javascript
app.directive('helloWorld', function() {

  return {

    scope: {

      color: '='

    },

    ....

    // the rest of the configurations

  };

});
```

相应的HTML修改如下：

```html
<body ng-controller="MainCtrl">

  <input type="text" ng-model="color" placeholder="Enter a color"/>

  <hello-world color="color"/>

</body>
```

**选择三：**使用 & 在父scope中执行函数与 @ 不同，这种方式让你能够给属性指定一个真实的scope数据模型，而不是简单的字符串。这样你就可以传递简单的字符串、数组、甚至复杂的对象给隔离scope。同时，还支持双向的绑定。每当父scope属性变化时，相对应的隔离scope中的属性也跟着改变，反之亦然。和之前的一样，你也可以监视这个scope属性的变化。

有时候从隔离scope中调用父scope中定义的函数是非常有必要的。为了能够访问外部scope中定义的函数，我们使用 &。比如我们想要从指令内部调用 sayHello() 方法。下面的代码告诉我们该怎么做：

```javascript
app.directive('sayHello', function() {

  return {

    scope: {

      sayHelloIsolated: '&amp;'

    },

    ....

    // the rest of the configurations

  };

});
```

相应的HTML代码如下：

```html
<body ng-controller="MainCtrl">

  <input type="text" ng-model="color" placeholder="Enter a color"/>

  <say-hello sayHelloIsolated="sayHello()"/>

</body>
```

父scope、子scope以及隔离scope的区别这个 [Plunker](http://plnkr.co/edit/k4scWKwtGBJw7lfKGqVJ?p=preview) 例子对上面的概念做了很好的诠释。

作为一个Angular的新手，你可能会在选择正确的指令scope的时候感到困惑。默认情况下，指令不会创建一个新的scope，而是沿用父scope。但是在很多情况下，这并不是我们想要的。如果你的指令重度地使用父scope的属性、甚至创建新的属性，会污染父scope。让所有的指令都使用同一个父scope不会是一个好主意，因为任何人都可能修改这个scope中的属性。因此，下面的这个原则也许可以帮助你为你的指令选择正确的scope。

1.父scope(scope: false) – 这是默认情况。如果你的指令不操作父scoe的属性，你就不需要一个新的scope。这种情况下是可以使用父scope的。

2.子scope(scope: true) – 这会为指令创建一个新的scope，并且原型继承自父scope。如果你的指令scope中的属性和方法与其他的指令以及父scope都没有关系的时候，你应该创建一个新scope。在这种方式下，你同样拥有父scope中所定义的属性和方法。

3.隔离scope(scope:{}) – 这就像一个沙箱！当你创建的指令是自包含的并且可重用的，你就需要使用这种scope。你在指令中会创建很多scope属性和方法，它们仅在指令内部使用，永远不会被外部的世界所知晓。如果是这样的话，隔离的scope是更好的选择。隔离的scope不会继承父scope。

### Transclusion（嵌入）

Transclusion是让我们的指令包含任意内容的方法。我们可以延时提取并在正确的scope下编译这些嵌入的内容，最终将它们放入指令模板中指定的位置。 如果你在指令定义中设置 transclude:true，一个新的嵌入的scope会被创建，它原型继承子父scope。 如果你想要你的指令使用隔离的scope，但是它所包含的内容能够在父scope中执行，transclusion也可以帮忙。

假设我们注册一个如下的指令：

```javascript
app.directive('outputText', function() {

  return {

    transclude: true,

    scope: {},

    template: '<div ng-transclude></div>'

  };

});
```

它使用如下：

```html
<div output-text>

  <p>Hello {{name}}</p>

</div>
```

transclude:’element’ 和 transclude:true的区别ng-transclude 指明在哪里放置被嵌入的内容。在这个例子中DOM内容 <p>Hello {{name}}</p> 被提取和放置到 <div ng-transclude></div> 内部。有一个很重要的点需要注意的是，表达式{{name}}所对应的属性是在父scope中被定义的，而非子scope。你可以在这个Plunker例子中做一些实验。如果你想要学习更多关于scope的知识，可以阅读[这篇文章](https://github.com/angular/angular.js/wiki/Understanding-Scopes)。

有时候我我们要嵌入指令元素本身，而不仅仅是它的内容。在这种情况下，我们需要使用 transclude:’element’。它和 transclude:true 不同，它将标记了 ng-transclude 指令的元素一起包含到了指令模板中。使用transclusion，你的link函数会获得一个名叫 transclude 的链接函数，这个函数绑定了正确的指令scope，并且传入了另一个拥有被嵌入DOM元素拷贝的函数。你可以在这个 transclude 函数中执行比如修改元素拷贝或者将它添加到DOM上等操作。 类似 ng-repeat 这样的指令使用这种方式来重复DOM元素。仔细研究一下这个Plunker，它使用这种方式复制了DOM元素，并且改变了第二个实例的背景色。

同样需要注意的是，在使用 transclude:’element’的时候，指令所在的元素会被转换成HTML注释。所以，如果你结合使用 transclude:’element’ 和 replace:false，那么指令模板本质上是被添加到了注释的innerHTML中——也就是说其实什么都没有发生！相反，如果你选择使用 replace:true，指令模板会替换HTML注释，那么一切就会如果所愿的工作。使用 replade:false 和 transclue:’element’有时候也是有用的，比如当你需要重复DOM元素但是并不想保留第一个元素实例（它会被转换成注释）的情况下。对这块还有疑惑的同学可以阅读stackoverflow上的[这篇讨论](http://stackoverflow.com/questions/18449743/when-to-use-transclude-true-and-transclude-element)，介绍的比较清晰。

### controller 函数和 require

如果你想要允许其他的指令和你的指令发生交互时，你需要使用 controller 函数。比如有些情况下，你需要通过组合两个指令来实现一个UI组件。那么你可以通过如下的方式来给指令添加一个 controller 函数。

```javascript
app.directive('outerDirective', function() {

  return {

    scope: {},

    restrict: 'AE',

    controller: function($scope, $compile, $http) {

      // $scope is the appropriate scope for the directive

      this.addChild = function(nestedDirective) { // this refers to the controller

        console.log('Got the message from nested directive:' + nestedDirective.message);

      };

    }

  };

});
```

JavaScript这个代码为指令添加了一个名叫 outerDirective 的controller。当另一个指令想要交互时，它需要声明它对你的指令 controller 实例的引用(require)。可以通过如下的方式实现：

```javascript
app.directive('innerDirective', function() {

  return {

    scope: {},

    restrict: 'AE',

    require: '^outerDirective',

    link: function(scope, elem, attrs, controllerInstance) {

      //the fourth argument is the controller instance you require

      scope.message = "Hi, Parent directive";

      controllerInstance.addChild(scope);

    }
  };
});
```

相应的HTML代码如下：

```html
<outer-directive>   <inner-directive></inner-directive> </outer-directive>
```

require: ‘^outerDirective’ 告诉Angular在元素以及它的父元素中搜索controller。这样被找到的 controller 实例会作为第四个参数被传入到 link 函数中。在我们的例子中，我们将嵌入的指令的scope发送给父亲指令。如果你想尝试这个代码的话，请在开启浏览器控制台的情况下打开这个Plunker。同时，[这篇Angular官方文档](http://docs.angularjs.org/guide/directive)上的最后部分给了一个非常好的关于指令交互的例子，是非常值得一读的。

### 一个记事本应用

这一部分，我们使用Angular指令创建一个简单的记事本应用。我们会使用HTML5的 localStorage 来存储笔记。最终的产品在[这里](http://embed.plnkr.co/QvxI4LbqfUY3C3XQjN3m/preview)，你可以先睹为快。

我们会创建一个展现记事本的指令。用户可以查看他/她创建过的笔记记录。当他点击 add new 按钮的时候，记事本会进入可编辑状态，并且允许创建新的笔记。当点击 back 按钮的时候，新的笔记会被自动保存。笔记的保存使用了一个名叫 noteFactory 的工厂类，它使用了 localStorage。工厂类中的代码是非常直接和可理解的。所以我们就集中讨论指令的代码。

### 第一步

我们从注册 notepad 指令开始。

```javascript
app.directive('notepad', function(notesFactory) {

  return {

    restrict: 'AE',

    scope: {},

    link: function(scope, elem, attrs) {

    },

    templateUrl: 'templateurl.html'

  };

});
```

因为我们想让指令可重用，所以选择使用隔离的scope。这个指令可以拥有很多与外界没有关联的属性和方法。这里有几点需要注意的：

- 这个指令可以以属性或者元素的方式被使用，这个被定义在 restrict 属性中。
- 现在的link函数是空的
- 这个指令从 templateurl.html 中获取指令模板

### 第二步

下面的HTML组成了指令的模板。

```html
<div class="note-area" ng-show="!editMode">

  <ul>

    <li ng-repeat="note in notes|orderBy:'id'">

      <a href="#" ng-click="openEditor(note.id)">{{note.title}}</a>

    </li>

  </ul>

</div>

<div id="editor" ng-show="editMode" class="note-area" contenteditable="true" ng-bind="noteText"></div>

<span><a href="#" ng-click="save()" ng-show="editMode">Back</a></span>

<span><a href="#" ng-click="openEditor()" ng-show="!editMode">Add Note</a></span>
```

note 对象中封装了 title，id 和 content。几个重要的注意点：

- ng-repeat 用来遍历 notes 中所有的笔记，并且按照自动生成的 id 属性进行升序排序。
- 我们使用一个叫 editMode 的属性来指明我们现在在哪种模式下。在编辑模式下，这个属性的值为 true 并且可编辑的 div 节点会显示。用户在这里输入自己的笔记。
- 如果 editMode 为 false，我们就在查看模式，显示所有的 notes。
- 两个按钮也是基于 editMode 的值而显示和隐藏。
- ng-click 指令用来响应按钮的点击事件。这些方法将和 editMode 一起添加到scope中。
- 可编辑的 div 框与 noteText 相绑定，存放了用户输入的文本。如果你想编辑一个已存在的笔记，那么这个模型会用它的文本内容初始化这个 div 框。

### 第三步

我们在scope中创建一个名叫 restore() 的新函数，它用来初始化我们应用中的各种控制器。 它会在 link 函数执行的时候被调用，也会在 save 按钮被点击的时候调用。

```javascript
scope.restore = function() {

  scope.editMode = false;

  scope.index = -1;

  scope.noteText = '';

};
```

### 第四步

我们在 link 函数的内部创建这个函数。 editMode 和 noteText 之前已经解释过了。 index 用来跟踪当前正在编辑的笔记。 当我们在创建一个新的笔记的时候，index 的值会设成 -1. 我们在编辑一个已存在的笔记的时候，它包含了那个 note 对象的 id 值。

现在我们要创建两个scope函数来处理编辑和保存操作。

```javascript
scope.openEditor = function(index) {

  scope.editMode = true;

  if (index !== undefined) {

    scope.noteText = notesFactory.get(index).content;

    scope.index = index;

  } else {

    scope.noteText = undefined;

  }

};

scope.save = function() {

  if (scope.noteText !== '') {

    var note = {};

    note.title = scope.noteText.length > 10 ? scope.noteText.substring(0, 10) + '. . .' : scope.noteText;

    note.content = scope.noteText;

    note.id = scope.index != -1 ? scope.index : localStorage.length;

    scope.notes = notesFactory.put(note);

  }

  scope.restore();

};
```

openEditor 为编辑器做准备工作。如果我们在编辑一个笔记，它会获取当前笔记的内容并且通过使用 ng-bind 将内容更新到可编辑的 div 中。这两个函数有几点需要注意：

- 如果我们在创建一个新的笔记，我们会将 noteText 设置成 undefined，以便当我们在保存笔记的时候，触发相应的监听器。
- 如果 index 参数是 undefined，它表明用户正在创建一个新的笔记。
- save 函数通过使用 notesFactory 来存储笔记。在保存完成后，它会刷新 notes 数组，从而监听器能够监测到笔记列表的变化，来及时更新。
- save 函数调用在重置 controllers 之后调用restore()，从而可以从编辑模式进入查看模式。

### 第五步

在 link 函数执行时，我们初始化 notes 数组，并且为可编辑的 div 框绑定一个 keydown 事件，从而保证我们的 nodeText 模型与 div 中的内容保持同步。我们使用这个 noteText 来保存我们的笔记内容。

```javascript
var editor = elem.find('#editor');

scope.restore();  // initialize our app controls

scope.notes = notesFactory.getAll(); // load notes

editor.bind('keyup keydown', function() {

  scope.noteText = editor.text().trim();

});
```

### 第六步

最后，我们在HTML如同使用其他的HTML元素一样使用我们的指令，然后开始做笔记吧。

```html
<h1 class="title">The Note Making App</h1>

<notepad/>
```

源码下载：[https://github.com/jsprodotcom/source/blob/master/AngularJS_Note_Taker-source_code.zip](https://github.com/jsprodotcom/source/blob/master/AngularJS_Note_Taker-source_code.zip)

### 总结

一个很重要的点需要注意的是，任何使用jQuery能做的事情，我们都能用Angular指令来做到，并且使用更少的代码。所以，在使用jQuery之前，请考虑一下我们能否在不进行DOM操作的情况下以更好的方式来完成任务。

转载自：[http://www.codeceo.com](http://www.codeceo.com/article/angularjs-command-learn.html)