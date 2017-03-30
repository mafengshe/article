
随着近些年JavaScript社区的不断发展，JavaScript语言有了很多新的特性。其中的一个好的方面就是通过Node.js渗透了服务端开发，这可以让JavaScript开发者更为强大。

今天，我希望讨论的是ECMA2015（ES6）规范中的三个特性，希望可以让你开始使用ES6。

本文不是ES6特性的详细介绍指南，它是对这些特性的书面介绍。因此，本文不会深入介绍这些特性，也不会列出每个特性的优点与缺点。

## 箭头函数

作为一个JavaScript开发人员，你一定知道原来的函数声明方式。

下面是一个简单的例子，它将时间戳转换成可读的形式。

```
function dateFromTimeStamp(timeStamp) {
  var humanReadableDate = new Date(timeStamp);
    return humanReadableDate;
}
dateFromTimeStamp(1489957823485); ////Sun Mar 19 2017 22:10:23 GMT+0100 (WAT)

```
ES2015中

```
var dateFromTimeStamp = timeStamp => new Date(timeStamp);
dateFromTimeStamp(1489957823485); ////Sun Mar 19 2017 22:10:23 GMT+0100 (WAT)

```

你可以点击[这里](http://wesbos.com/arrow-functions/)学习更多关于箭头函数的内容。

## 模板字符串

对我而言，这是一个最重要的特征，它使得我的字符串更易理解和接受。

```
var friendOne = 'Smith';
var friendTwo = 'Olawale';
var friendThree = 'Victor';

var allOfThem = 'The three friends are ' + friendOne + ', ' + friendTwo + ', and' + friendThree; 
//Output:  'The three friends are Smith, Olawale, andVictor'
```
ES2015中

```
var friendOne = 'Smith';
var friendTwo = 'Olawale';
var friendThree = 'Victor';

var allOfThem = `The three friends are ${friendOne}, ${friendTwo} and ${friendThree}`;
//Output:  'The three friends are Smith, Olawale and Victor'
```
从上边这个例子可以看出，使用ES6模板字符串让编码更轻松，代码更简洁。我相信我不是唯一一个认为旧的解决方案像是hack。另一个好处是，模板支持多行文字，你可以写多行字符串，如果你换行，JavaScript会自动加上\n。
例如：

```
var author = 'Olawale';
var poem = `
I am ${author}, this is my poem
There isn't much to say
And here is my last line
`;
//Output: '\nI am Olawale, this is my poem\nThere isn\'t much to say\nAnd here is my last line\n'
```
你可以点击[这里](http://exploringjs.com/es6/ch_template-literals.html)学习更多关于模板字符串的内容。

## 默认参数
在ES2015之前，如果你想要在函数中指定默认参数，你必须像在下边这个例子中这样：

```
function greetHuman(name) {
name = name || 'human';
return 'Hello ' + name + ', we come in peace'; 
}
greetHuman(); // 'Hello human, we come in peace'
greetHuman('Olawale'); // 'Hello Olawale, we come in peace'
```
通过ES6，你可以再函数头处实现，不需要在函数体内检查参数。

让我们来看下面这个例子，这个例子用到了箭头函数、模板字符串和默认参数：

```
const greetHuman = (name = 'human') => 'Hello ${human}, we come in peace';
greetHuman('Olawale'); 'Hello Olawale, we come in peace'
greetHuman(); 'Hello human, we come in peace'
```

你可以点击[这里](http://exploringjs.com/es6/)学习更多的ES6特性。以下是其他的学习ECMA(ES6)的资源：
- [How to Get Started with learning ES6](https://www.codementor.io/javascript/tutorial/learn-ecmascript6)
- [Introduction to ES6](https://www.codementor.io/javascript/tutorial/qa-es6-vs-typescript-debugging-workflow)
- [The Ultimate JavaScript Cheat Sheet](https://www.codementor.io/johnnyb/javascript-cheatsheet-fb54lz08k)

原文链接：[Javascript ES6: 3 Cool Features You Should Be Using](https://www.codementor.io/brainyfarm/javascript-es6-3-cool-features-you-should-be-using-6bntdqk5p)





