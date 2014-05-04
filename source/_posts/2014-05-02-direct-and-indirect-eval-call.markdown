---
layout: post
title: "Direct and indirect eval call"
date: 2014-05-02 00:49:09 +0300
comments: true
categories: javascript
---
今天看到javascript design pattern中关于如何  [access global object], 代码如下：
```javascript
var global = (function () {
				return this || (1, eval)('this');
			}());
```
这代码的意思是无论在non-strict mode和strict mode都可以拿到global这个对象。但是`(1, eval)('this')`这行代码究竟是什么写法，对1到底代表什么非常不解，stackoverflow上面有说是这行代码表示indirect eval call，而direct和indirect eval的区别是direct的this是指闭包的this，indirect的this则是全局的this就好像这个方程是在全局被调用一样。还是没解释（1，eval）这种写法到底是什么意思，于是又分别读到了[全局eval]、JavaScript: The definitive guide的[4.12]章和[js的符号及术语]的定义。

找了半天，原来和`，`在js的定义有关（大汗。。。）：
>The comma operator evaluates each of its operands (from left to right) and returns the value of the last operand.

意思是：`，`逗号会从左到右evaluate每个运算对象，返回最后一个运算对象的值。所以（1,eval）还是返回了eval。至于究竟是直接还是间接调用，ES5对直接eval的定义： “eval” in CallExpression should be a Reference, not a value。

于是又涉及到一个名词：CallExpression，和一个问题：怎么判断eval是reference还是value？

先看什么是CallExpression：
```

  (     1        ,         eval  )        ( '1+1' )
     |____|   |_____|    |_____|
     Literal  Operator   Identifier

     |_________________________|
     Expression

  |______________________________|
  PrimaryExpression

  |______________________________|        |________|
  MemberExpression                        Arguments

  |________________________________________________|
  CallExpression
```
reference还是value？
先看几个例子
```javascript
 (((eval)))(); //直接
 (1,eval)(); // 间接
 (eval = eval)(); // 间接
```
原因是在ES5定义里，`，`和`=`这类操作符会执行运算对象的`GetValue`方法，而 Grouping Operator — `( )` 不会evaluate传入的expression，如果传入的是reference则整个括号内内容不变，将还是一个reference。

再看上面提到`GetValue`这个玩意儿。根据官方文档：
GetValue (V)

[access global object]: https://github.com/shichuan/javascript-patterns/blob/master/general-patterns/access-to-global-object.html
[全局eval]: http://perfectionkills.com/global-eval-what-are-the-options/
[js的符号及术语]: http://www.ecma-international.org/ecma-262/5.1/#sec-11.2
[4.12]:https://www.inkling.com/read/javascript-definitive-guide-david-flanagan-6th/chapter-4/evaluation-expressions
