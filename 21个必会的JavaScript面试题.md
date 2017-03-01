
## JavaScript 中 `undefined` 和 `not defined` 的区别

JavaScript 未声明变量直接使用会抛出异常：`var name is not defined`，如果没有处理异常，代码就停止运行了。
但是，使用`typeof undeclared_variable`并不会产生异常，会直接返回 `undefined`。

```js
var x; // 声明 x
console.log(x); //output: undefined 
```

```js
console.log(typeof y); //output: undefined 
```

```js
console.log(z);  // 抛出异常: ReferenceError: z is not defined
```

## 下面的代码输出什么？

```js
var y = 1;
if (function f(){}) {
    y += typeof f;
}
console.log(y);
```

正确的答案应该是 `1undefined`。

JavaScript中if语句求值其实使用`eval`函数，`eval(function f(){})` 返回 `function f(){}` 也就是 `true`。

下面我们可以把代码改造下，变成其等效代码。

```js
var k = 1;
if (1) {
    eval(function foo(){});
    k += typeof foo;
}
console.log(k); 
```
上面的代码输出其实就是 `1undefined`。为什么那？

和以下代码不同。
```js
var k = 1;
if (1) {
    function foo(){};
    k += typeof foo;
}
console.log(k); // output 1function
```

