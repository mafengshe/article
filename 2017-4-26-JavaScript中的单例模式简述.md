## 定义
单例模式是一种常用的软件设计模式。在它的核心结构中只包含一个被称为单例的特殊类。通过单例模式保证系统中一个类只有一个实例。

## 基本实现方式

实现单例最简单的方式是创建对象字面量：

```
var Singleton = {
    attribute1: true,
    attribute2: 10,
    method:function(){
        console.log("A simple singleton example");
    }
}
```

## 实例

我们可以用闭包来实现拥有私有变量的单例对象。

```
var Singleton = (function(){
    var instance;
    function initInstance(){
        var object = new Object();
        object.msg = "Singleton instance";
        return object;
    }
    return {
        getInstance:function(){
            if(!instance){
                instance = initInstance();
            }
            return instance;
        }
    };
})();

var instance1 = Singleton.getInstance();
var instance2 = Singleton.getInstance();
instance1 === instance2;//true
```
在上边这个例子中，instance只有一个实例，都是通过Singleton.getInstance()方法得到。其中的instance作为私有变量，initInstance()是一个私有方法，getInstance()方法可以在外部访问到，并且多次调用返回都是同一个实例。

## 单例模式用途

### 划分命名空间

单例对象是全局的，可以将一些成员封装在这个对象中，这些成员只能通过这个单例对象进行访问。我们可以将其看作是将这些成员组织在了一个命名空间中，这样可以避免无意间改写变量。

例如，在你的网站有有很多网页，有一部分JavaScript代码是通用的，可以被存放在独立的文件中，有些代码是单个网页专用的，可以把这些代码分别封装在自己的单例对象中。这样可以将代码分离开来，避免冲突。同时也能与你所引用的其它库代码分离开来。

### 分支

> 分支是一种用来把浏览器间的差异封装到在运行期间进行设置的动态方法的技术。

使用分支技术，在脚本加载时一次性确定针对特定浏览器的代码，在初始化完成之后，每种浏览器只会执行针对它的JavaScript代码。

比如说，你在创建ajax对象时需要根据浏览器的版本和类型使用不同的创建方法，那么这个时候我们就可以用到分支了。使用分支方法在初次加载JavaScript代码时，就确定创建ajax对象的方法，不需要每次都去判断。

## 框架中的单例模式
以下代码来自https://github.com/nodejs/node/blob/73ae2d1895c2c0d1d4eeaddd284c58d776d2be87/deps/v8/test/mjsunit/es6/arraybuffer-species.js

```
class SingletonArrayBuffer extends ArrayBuffer {
  constructor(...args) {
    if (SingletonArrayBuffer.cached) return SingletonArrayBuffer.cached;
    super(...args);
    SingletonArrayBuffer.cached = this;
  }
}
assertThrows(() => new SingletonArrayBuffer(5).slice(0, 4), TypeError);

```
上面这个例子中SingletonArrayBuffer.cached是一个单例对象，获取这个对象的时候，如果已经存在，返回当前存在的对象，如果不存在，将this赋给这个对象。

同时单例模式也是JavaScript中很常见的一种设计模式，更好地理解单例模式，学会在你自己的代码中用单例模式，提高代码可读性与稳定性。

## 单例模式的优点

 - 很好地组织代码，把相关方法和属性组织在一个单例对象中。

 - 防止全局命名空间被变量污染，提高网页稳定性。

## 单例模式的缺点

 - 它有可能导致模块之间的强耦合。

