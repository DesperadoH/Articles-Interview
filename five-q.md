# 5个典型的JavaScript面试题

http://web.jobbole.com/80564/

## 问题1：作用域

```
(function() {
 
var a = b = 5;
 
})();
 
console.log(b);
```

请问控制台上会输出什么？

答案

输出：5

这一题的陷阱是，在函数表达式中有两个赋值，但a是用关键字var 来声明的，这意味着a是局部变量，而b则被赋予为全局变量。

另一个陷阱是，它并没有使用严格模式（use strict）。在函数里面，如果启用了严格模式，代码就会报错：“Uncaught ReferenceError: b is not defined”。请记住，严格模式需要你显式地引用全局作用域，代码应该写成：

```
(function() {
 
'use strict';
 
var a = window.b = 5;
 
})();
 
console.log(b);

```

## 问题2：创建“内置”方法

给String对象定义一个repeatify方法。该方法接收一个整数参数，作为字符串重复的次数，最后返回重复指定次数的字符串。例如：

```
console.log('hello'.repeatify(3));
```

输出应该是

```
hellohellohello.
```

一个可行的做法如下：

```

String.prototype.repeatify = String.prototype.repeatify || function(times) {
 
var str = '';
 
for (var i = 0; i < times; i++) {
 
str += this;
 
}
 
return str;
 
};
```

这题测试开发者对Javascript的继承及原型属性的知识，它同时也检验了开发者是否能扩展内置数据类型的方法。

这里的另一个关键点是，看你怎样避免重写可能已经定义了的方法。这可以通过在定义自己的方法之前，检测方法是否已经存在。

```
String.prototype.repeatify = String.prototype.repeatify || function(times) {/* code here */};

```

当你被问起去扩展一个Javascript方法时，这个技术非常有用

## 问题3 ：声明提前


```
function test() {
 
console.log(a);
 
console.log(foo());
 
var a = 1;
 
function foo() {
 
return 2;
 
}}
 
test();
```

答案

代码的运行结果：undefined和 2

理由是，变量和函数的声明都被提前至函数体的顶部，而同时变量并没有被赋值。因此，当打印变量a时，它虽存在于函数体（因为a已经被声明），但仍然是undefined。换句话说，上面的代码等同于下面的代码：

```
function test() {
 
var a;
 
function foo() {
 
return 2;
 
}
 
console.log(a);
 
console.log(foo());
 
a = 1;
 
}
 
test();

```

## 问题4：JavaScript中的this

下面代码的运行结果是什么并做解释。

```

var fullname = 'John Doe';
 
var obj = {
 
fullname: 'Colin Ihrig',
 
prop: {
 
fullname: 'Aurelio De Rosa',
 
getFullname: function() {
 
return this.fullname;
 
}}};
 
console.log(obj.prop.getFullname());
 
var test = obj.prop.getFullname;
 
console.log(test());

```

答案

代码输出：Aurelio De Rosa 和 John Doe

理由是，Javascript中关键字this所指代的函数上下文，取决于函数是怎样被调用的，而不是怎样被定义的。

在第一个console.log()，getFullname()被作为obj.prop对象被调用。因此，当前的上下文指代后者，函数返回这个对象的fullname属性。相反，当getFullname()被赋予test变量，当前的上下文指代全局对象window，这是因为test被隐式地作为全局对象的属性。基于这一点，函数返回window的fullname，在本例中即为代码的第一行。


## 问题5：call()和apply()

修复前一个问题，让最后一个console.log() 打印输出Aurelio De Rosa.

答案

这个问题可以通过运用call（）或者apply()方法强制转换上下文环境。如果你不了解这两个方法及它们的区别，我建议你看看这篇文章 What’s the difference between function.call and function.apply?. 下面的代码中我用了call(),但apply（）也能产生同样的结果：

```
console.log(test.call(obj.prop));
```








