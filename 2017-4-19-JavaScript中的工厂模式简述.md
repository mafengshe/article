
## 定义

> 工厂模式定义创建对象的接口，但是让子类决定实例化哪个类。工厂方法将类的实例化延迟到子类。

## 概述

我们可以使用Object构造函数来创建单个对象，但是，使用同一个接口创建很多对象时，会产生大量重复的代码。为了解决这个问题，我们可以使用工厂模式。

## 实例

我们来看一个简单的例子：

```
var employee1 = new Object();
employee1.position = "Front end engineer";
employee1.tool = "I love vscode.";
employee1.introduction = function () {
    console.log("I am a " + this.position + ", and " + this.tool);
}
var employee2 = new Object();
employee2.position = "UI designer";
employee2.tool = "I love photoshop.";
employee2.introduction = function () {
    console.log("I am a " + this.position + ", and " + this.tool);
}
employee1.introduction();//I am a Front end engineer, and I love vscode.
employee2.introduction();//I am a UI designer, and I love photoshop.
```
在上边这个例子中，我们定义了两个employee，一个是Front End Engineer，另一个是UI designer，他们都有position属性和tool属性，也都有introduction方法。如果我们需要创建很多个类似employee的对象呢，那我们就需要重复很多类似的代码。接下来，我们做一些简单的修改：

```
function Employee(type) {
    var employee;
    if (type == "programmer") {
        employee = new Programmer();
    } else if (type == "designer") {
        employee = new Designer();
    }
    employee.introduction = function () {
        console.log("I am a " + this.position + ", and " + this.tool);
    }
    return employee;

}

function Programmer() {
    this.position = "Front end engineer";
    this.tool = "I love vscode.";
}
function Designer() {
    this.position = "UI designer";
    this.tool = "I love photoshop.";
}

var employee1 = Employee("programmer");
employee1.introduction();//I am a Front end engineer, and I love vscode.
var employee2 = Employee("designer");
employee2.introduction();//I am a UI designer, and I love photoshop.

```
在上边这段代码中，我们将employee的初始化分别放到了Programmer()和Designer()中实现。这其实就是一个简单工厂模式的例子，Employee是一个工厂，可以根据传入的type的不同，创建不同的employee，每个employee有自己的职位和使用的工具，每个employee都可以介绍自己的这些信息。

## 框架中的工厂模式

jQuery中的$()其实就是一个工厂函数，它根据传入参数的不同创建元素或者去寻找上下文中的元素，创建成相应的jQuery对象。
以下实例来自于https://github.com/jquery/jquery/blob/master/src/core/init.js
```
init = jQuery.fn.init = function( selector, context, root ) {
    var match, elem;

	// HANDLE: $(""), $(null), $(undefined), $(false)
	if ( !selector ) {
		return this;
	}

	// Method init() accepts an alternate rootjQuery
	// so migrate can support jQuery.sub (gh-2101)
	root = root || rootjQuery;

	// Handle HTML strings
	if ( typeof selector === "string" ) {
			
        //...

	// HANDLE: $(DOMElement)
	} else if ( selector.nodeType ) {
        
		//....

	// HANDLE: $(function)
	// Shortcut for document ready
	} else if ( jQuery.isFunction( selector ) ) {
		//....
	}

	return jQuery.makeArray( selector, this );
};
```
同时，像Angular2、Node、Vue、React等等，很多开源框架中其实都用到了工厂模式，学会工厂模式，有助于你更好的理解和使用这些框架。

## 总结

当需要根据不同参数产生不同实例，这些实例都有相同的行为，这时候我们可以使用工厂模式，简化实现的过程，同时也可以减少每种对象所需的代码量。工厂模式有利于消除对象间的耦合，提供更大的灵活性。

注：如果不需要另外一个类，或者不需要在运行期间判断实例化的对象属于哪个类，那就不需要使用工厂模式，大多数情况下使用new关键字和构造函数公开实例化对象，提高代码可读性。切勿滥用。

