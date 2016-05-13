# another

http://web.jobbole.com/81785/

## 问题1：闭包

```
var nodes = document.getElementsByTagName('button');
for (var i = 0; i < nodes.length; i++) {
   nodes[i].addEventListener('click', function() {
      console.log('You clicked element #' + i);
   });
}
```

请问，如果用户点击第一个和第四个按钮，控制台上会输出什么？为什么？

答案

上面代码的目的在于检测JavaScript的一个重要概念：闭包。对于每一个JavaScript开发者来说，如果你想在网页中编写5行以上的代码，那么准确理解和恰当使用闭包是非常重要的。如果你想开始学习或者只是想简单地温习一下闭包，那么我强烈建议你去阅读这个教程：Colin Ihrig 写的JavaScript Closures Demystified 。

好了，回到上面的代码。控制台会输出两次You clicked element #NODES_LENGTH，其中#NODES_LENGTH等于nodes的结点个数。由于闭包中变量的值不是静态的，i的值并不是添加click事件处理器时的值（比如，当给第一个button添加click事件处理器时i为0，给第二个添加时i为1）。当for循环结束时，变量i的值等于nodes的长度。因此事件被执行时，控制台会输出变量i当前的值，即等于nodes的长度。

## 问题2：闭包

修复上题的问题，使得点击第一个按钮时输出0，点击第二个按钮时输出1。

答案

有多种办法解决这个问题，下面我给出其中的两种。

第一个解决方案要用到一个IIFE来创建另外一个闭包，从而得到所希望的i的值。相应的代码如下：

```
var nodes = document.getElementsByTagName('button');
for (var i = 0; i < nodes.length; i++) {
   nodes[i].addEventListener('click', (function(i) {
      return function() {
         console.log('You clicked element #' + i);
      }
   })(i));
}

```

另一个解决方案不使用IIFE，而是将函数移到循环的外面，代码如下：

```

function handlerWrapper(i) {
   return function() {
      console.log('You clicked element #' + i);
   }
}
 
var nodes = document.getElementsByTagName('button');
for (var i = 0; i <nodes.length; i++) {
   nodes[i].addEventListener('click', handlerWrapper(i));
}

```

## 问题3：数据类型

```
console.log(typeof null);
console.log(typeof {});
console.log(typeof []);
console.log(typeof undefined);

```

答案

这一个问题看起来似乎有点傻，但是它测试了typeof 操作符的知识。很多JavaScript开发者并没有意识到typeof的独特性。在本例中，控制台会输出下面的内容：

```
object
object
object
undefined

```

最让人吃惊的输出结果可能是第三个，许多开发者认为typeof [ ] 会返回Array。如果你想测试变量是否为数组，可以写下面的代码：

```
var myArray = [];
if (myArray instanceof Array) {
   console.log("myArray is an instance of Array.");
} 
else{
   console.log("myArray is not an instance of Array.");
}

```

## 问题4：事件循环

下面代码运行结果是什么？请解释。


```
function printing() {
   console.log(1);
   setTimeout(function() { console.log(2); }, 1000);
   setTimeout(function() { console.log(3); }, 0);
   console.log(4);
}
printing();
```

答案

输出结果：

```
1
4
3
2
```

要弄懂数字为何以这种顺序输出，你需要弄明白setTimeout()是干什么的，以及浏览器的事件循环工作原理。浏览器有一个事件循环用于检查事件队列，处理延迟的事件。UI事件（例如，点击，滚动等），Ajax回调，以及提供给setTimeout()和setInterval()的回调都会依次被事件循环处理。因此，当调用setTimeout()函数时，即使延迟的时间被设置为0，提供的回调也会被排队。回调会呆在队列中，直到指定的时间用完后，引擎开始执行动作（如果它在当前不执行其他的动作）。因此，即使setTimeout（）回调被延迟0毫秒，它仍然会被排队，并且直到函数中其他非延迟的语句被执行完了之后，才会执行。

有了这些认识，理解输出结果为“1”就容易了，因为它是函数的第一句并且没有使用setTimeout()函数来延迟。接着输出“4”，因为它是没有被延迟的数字，也没有进行排队。然后，剩下了“2”，“3”，两者都被排队，但是前者需要等待一秒，后者等待0秒（这意味着引擎完成前两个输出之后马上进行）。这就解释了为什么“3”在“2”之前。

【译者推荐扩展阅读：阮一峰文章JavaScript 运行机制详解：再谈Event Loop  】


## 问题5：算法

写一个判断质数的isPrime()函数，当其为质数时返回true，否则返回false。

答案

我认为这是在面试中最常问到的一个问题。尽管这个问题反复出现并且也很简单，但是从候选人提供的答案中能很好地看出候选人的数学和算法能力水平。

首先， 因为JavaScript不同于C或者Java，因此你不能信任传递来的数据类型。如果面试官没有明确地告诉你，你应该询问他是否需要做输入检查，还是不进行检查直接写函数。严格上说，应该对函数的输入进行检查。

需要记住的第二点，负数不是质数。同样的，1和0都不是，因此，要对这些数字做检测。另外，2是唯一的既是偶数又是质数的数字。没有必要用一个循环来验证4,6,8。再者，如果一个数字不能被2整除，它同样也不能被4,6,8等整除，因此你的循环需要跳过这些数字。可以采取其他一些更明智的优化手段，我这里采用的是适用于大多数情况的。例如，如果一个数字不能被5整除，它也不会被5的倍数整除。所以，没有必要检测10,15,20等等。如果你深入了解这个问题的解决方案，我建议你去看相关的Wikipedia介绍。

最后一点，你不需要检查比输入数字的开方还要大的数字。我感觉人们会遗漏掉这一点，并且也不会因为此而获得消极的反馈。但是，展示出这一方面的知识会给你额外加分。

现在你具备了这个问题的背景知识，下面是总结以上所有考虑的解决方案：

```
function isPrime(number) {
   // If your browser doesn't support the method Number.isInteger of ECMAScript 6,
   // you can implement your own pretty easily
   if (typeof number !== 'number' || !Number.isInteger(number)) {
      // Alternatively you can throw an error.
      return false;
   }
   if (number < 2) {
      return false;
   }
 
   if (number === 2) {
      return true;
   } else if (number % 2 === 0) {
      return false;
   }
   var squareRoot = Math.sqrt(number);
   for(var i = 3; i <= squareRoot; i += 2) {
      if (number % i === 0) {
         return false;
      }
   }
   return true;
}

```



















