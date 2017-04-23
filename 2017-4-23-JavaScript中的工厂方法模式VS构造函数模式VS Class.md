原文链接：https://medium.com/javascript-scene/javascript-factory-functions-vs-constructor-functions-vs-classes-2f22ceddf33e

在ES6之前，JavaScript中的工厂函数和构造函数之间的差异令许多人困惑。由于ES6有了“class”关键字，很多人认为它解决了很多构造函数的问题。其实并没有。让我们来了解一下你仍然需要注意的事项。

我们首先来看一个例子：

```
// class
class ClassCar {
  drive () {
    console.log('Vroom!');
  }
}

const car1 = new ClassCar();
console.log(car1.drive());


// constructor
function ConstructorCar () {}

ConstructorCar.prototype.drive = function () {
  console.log('Vroom!');
};

const car2 = new ConstructorCar();
console.log(car2.drive());


// factory
const proto = {
  drive () {
    console.log('Vroom!');
  }
};

function factoryCar () {
  return Object.create(proto);
}
const car3 = factoryCar();
console.log(car3.drive());
```
每种方式都用到了原型，并且有选择地使用构造函数来创建私有变量。换句话说，它们有很多相同的特性，大多数情况下都可以互换使用。

> In JavaScript, any function can return a new object. When it’s not a constructor function or class, it’s called a factory function.

ES6的class是构造函数的语法糖，所以适用于构造函数的内容也适用于ES6的class：

```
class Foo {}
console.log(typeof Foo); // function
```
## 构造函数和class的优点

 - 大部分书都会教你使用class或者是构造函数
 - 'this'指向创建的新对象
 - 有些人喜欢myFoo = new Foo()这样的写法
 - 可能会有一些性能上的微弱优势，但是基本不需要担心，除非你对代码进行了分析并且证明这些差距对你而言非常重要。

## 构造函数和class的缺点

1. 需要new

在ES6之前，忘记new是一种常见的bug。很多人都会用样板来解决这个问题：

```
function Foo() {
  if (!(this instanceof Foo)) { return new Foo(); }
}
```
在ES6(ES2015)中，如果你调用构造函数和class的时候忘记了new，会抛出错误。如果不将class包装在工厂函数中，那么难以避免强迫调用者使用new。也有人建议在未来的JavaScript版本中可以允许调用者自定义调用行为时可以省略new，但这也意味着会给每个使用它的class增加额外开销（也意味着很少使用它）。

2. 实例化的细节被泄漏到了调用它的API（通过new）

所有的调用者都和构造函数的实现紧密耦合。如果你需要工厂的额外的灵活性，重构是一种突破性的变化。class到工厂的重构是常见的，他们出现在了Martin Fowler，Kent Beck，John Brant，William Opdyke和Don Roberts的创新重构的书：[《Refactoring: Improving the Design of Existing Code》](https://www.amazon.com/Refactoring-Improving-Design-Existing-Code/dp/0201485672/ref=as_li_ss_tl?ie=UTF8&linkCode=ll1&tag=eejs-20&linkId=7cca2e0acb03cf4b88c1f888c43aa24d)中。

3. 构造函数违背了开闭原则

由于使用了new，构造器函数违背了开闭原则：接口对扩展开放，对修改关闭。

我认为class到工厂的重构是非常普遍的，它应该被作为构造函数扩展的标准。从class到工厂的升级本来不应该打破什么，但是在JavaScript中，它会。

如果你已经开始导出构造函数或是类，并且用户开始使用构造函数，慢慢你会意识到工厂的灵活性是非常重要的（例如：选择对象池来实现，或在执行上下文中实例化对象、或使用可选择的原型以获得更多的灵活性），你不能达到目标除非强制调用者重构。

不幸的是，在JavaScript中，从构造函数或class切换到工厂需要打破这种变化。

```
// Original Implementation:

// class Car {
//   drive () {
//     console.log('Vroom!');
//   }
// }

// const AutoMaker = { Car };

// Factory refactored implementation:
const AutoMaker = {
  Car (bundle) {
    return Object.create(this.bundle[bundle]);
  },

  bundle: {
    premium: {
      drive () {
        console.log('Vrooom!');
      },
      getOptions: function () {
        return ['leather', 'wood', 'pearl'];
      }
    }
  }
};

// The refactored factory expects:
const newCar = AutoMaker.Car('premium');
newCar.drive(); // 'Vrooom!'

// But since it's a library, lots of callers
// in the wild are still doing this:
const oldCar = new AutoMaker.Car();

// Which of course throws:
// TypeError: Cannot read property 'undefined' of
// undefined at new AutoMaker.Car
```
在上边这个例子中，我们开始时使用了class，但是我们想要提高可用性，增加不同的车子类型。为了实现这个目标，工厂为不同的车子提供了可选择的prototype。我曾经用这项技术实现了不同的媒体播放器的接口，根据需要控制的播放器来选择正确的prototype。

4. 使用构造函数会导致“instanceof”的欺骗性

由构造器向工厂的重构其中一种突破性变化就是‘instanceof’。有时人们会试图用“instanceof”来检查代码中的数据类型。这就会导致问题，我建议你避免使用“instanceof”


```
// instanceof is a prototype identity check.
// NOT a type check.

// That means it lies across execution contexts,
// when prototypes are dynamically reassigned,
// and when you throw confusing cases like this
// at it:

function foo() {}
const bar = { a: 'a'};

foo.prototype = bar;

// Is bar an instance of foo? Nope!
console.log(bar instanceof foo); // false

// Ok... since bar is not an instance of foo,
// baz should definitely not be an instance of foo, right?
const baz = Object.create(bar);

// ...Wrong.
console.log(baz instanceof foo); // true. oops.
```
“instanceof”并没有做到你期望的类型检查。相反，它进行了身份认证，将对象的prototype对象与构造器的prototype属性进行比较。

在执行上下文中是不会起作用的例如（通常是bug、沮丧和不必要的限制的原因）。如果你的构造函数的prototype被替换，也不会发挥作用。

如果你在把构造函数转换成工厂方法的时候，使用class或者构造函数（返回“this”，与构造函数的prototype属性相关），然后切换到任意对象（没有与构造函数的prototype属性相关），也会导致失败。

简而言之，“instanceof”是从构造函数切换到工厂方法时的另一种突破性的变化。

## 使用class的优点

 - 简便、独立的语法
 - 单一的、规范的模仿JavaScript中类的方法。在ES6之前，还有几种流行库中的不同的实现方法。
 - 人们更加熟悉基于类的语言。

## 使用类的缺点

所有构造函数的缺点，还有：
 - 诱惑用户使用extend关键字创建多层次的class，很容易导致问题。

多层次的class会导致面向对象中一些众所周知的问题，包括：脆弱的基类问题、大猩猩香蕉问题、必要性导致的重复问题等等。不幸的是，class提供了像球可以投掷、椅子可以坐这样的扩展。更多详情，请阅读 [“The Two Pillars of JavaScript: Prototypal OO”](https://medium.com/javascript-scene/the-two-pillars-of-javascript-ee6f3281e7f3) 和 [“Inside the Dev Team Death Spiral”](https://medium.com/javascript-scene/inside-the-dev-team-death-spiral-6a7ea255467b)两篇文章。

构造函数和工厂都可以用来创建多层次的结构，但是class可以用extend关键字导致你走向错误的方向。换句话说，它鼓励你考虑不灵活的（通常是错的）is-a关系，而不是更灵活的has-a或者是can-do成分关系。

> 另一个提供的特性是支持执行特定行为的机会。例如，旋钮可以旋转，杠杆可以拉动，按钮可以按压等等。

## 使用工厂的优点

工厂比构造函数和class更加灵活，同时也不会用extend关键字和层次继承引导人们走向错误的方向。你可以使用不同的方法从工厂方法继承。特别是，用组合工工厂函数检查[邮票规格](https://github.com/stampit-org/stamp-specification/tree/de21b0ce53d5a31725f47c9ce2f77c9edcd9cdca)。

1. 返回任意对象，使用任意原型

例如：你可以很容易创建实现了同样接口的不同的对象。一种媒体播放器，它可以实例化多种视频的播放，这些视频在引擎下使用不同的API，它们的事件库使用了不同的Dom事件或web socket事件。

工厂函数也会根据执行上下文实例化对象，利用对象池的优势，这允许更灵活的基于原型的继承。

2. 不用担心重构

你从来都不需要把工厂转换为构造函数，所以重构不会是问题。

3. 不需要new

new没有歧义，不要使用（这会造成this指向不明确，请看下一点）。

4. 标准的this

this指向出了它该指向的，所以你可以通过它来获取父对象。例如：player.create()，this指向了player，就像call和apply方法确定了this的指向。

5. 不会有欺骗instanceof

6. 有些人喜欢“myFoo=createFoo()”这种方式

## 工厂方法的缺点

- 不会创建对象与工厂的prototype之间的联系，但这实际上是一个好事情，因为你不会被instanceof欺骗。相反，isntanceof会失败。请看优点。
- this没有指向工厂中的新对象。请看优点。
- 在微优化的基准测试中，它可能比构造函数的执行速度慢。如果非要测试的话，请确保你需要关注这个问题。

## 总结
在我看来，class确实有简便的语法，但是实际上它可能会诱导用户创造继承的class。这也是有风险的，在未来，你可能会升级为工厂，但是由于new关键字的使用，所有的调用者都与你的构造函数紧密耦合，从类转换为工厂，会导致突破性的变化。

你可能考虑你只需要重构调用部分，但是在大型团队中，或者你的class是公共API的一部分，你不可能去修改不在你控制下的代码。换句话说，你不能总是假设重构调用者是可能的选项。

工厂模式不仅仅更加强大灵活，同时也会鼓励整个团队、所有的API用户使用一种简单、灵活安全的模式。
关于工厂模式其实还有很多内容，尤其是关于使用邮票规格进行对象组合的效用。更多关于这个话题，以及与class的不同，请阅读[“3 Different Kinds of Prototypal Inheritance”](https://medium.com/javascript-scene/3-different-kinds-of-prototypal-inheritance-es6-edition-32d777fa16c9)。



