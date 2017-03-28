# JavaScript原型

每一个JavaScript对象(null除外)都和另一个对象相关联，“另一个”对象就是我们熟知的原型，每一个对象都从原型继承属性。

所有通过对象直接创建的对象都具有同一个原型对象，并可以通过JavaScript代码Object.prototype获得原型对象引用。通过关键字new和构造函数调用创建的对象的原型就是构造函数的prototype属性的值。

没有原型的对象为数不多，Object.prototype就是其中之一，不继承任何属性。其他原型对象都是普通对象，普通对象都有原型。所有内置构造函数（以及大部分自定义的构造函数）都具有一个继承自Object.prototype的原型。例如：使用{}创建对象、通过new Object()创建的对象都继承自Object.prototype。同样，通过new Array()创建的对象的原型就是Array.prototype，通过new Date()创建的对象原型就是Date.prototype。

## 原型链
由new Date()创建的Date对象的属性同时继承自Date.prototype和Object.prototype。这一系列链接的原型对象就是所谓的“原型链”。

JavaScript对象具有“自有属性”，也有一些属性是从原型对象继承而来的。我们来看一个简单的例子。

```
var o={x:1};
//Object.create()方法使用指定的原型对象和属性创建了一个新的对象
var p=Object.create(o);
p.y=2;
var q=Object.create(p);
q.z=3;
q.x+q.y //=3
```
为对象q的属性z赋值时，如果q中存在z属性（这个属性不是继承来的），这个赋值操作只会改变这个已有属性z的值，如果q中不存在属性z，将给q添加一个新的属性z，如果
q已经继承了属性z，那就这个继承的属性会被新创建的同名属性覆盖了。

在查询对象q上的属性时，首先判断q中是否存在属性，例如，在查询属性z时，首先判断对象q是否存在属性z，存在则返回属性z的值3，查询属性x时，在对象q中找不到属性x，就在它的原型链上寻找，在o中找到了属性x，并返回x的值，查询y属性时，在原型链中的p对象上找到了属性y，返回y的值，可以计算出q.x+q.y=3。


```
o.r=4;
q.r;//=>4
```
上边这个例子，我们可以看出原型的继承是动态的，创建了q对象之后，我们为o对象添加了新的属性，我们可以在q中访问到这个新的属性r。

## 利用原型创建自定义类型


```
function Book(name,writer,price){
    this.name=name;
    this.writer=writer;
    this.price=price;
}

Book.prototype={
    constructor:Book,
    sayBook:function(){
        console.log("BookName："+this.name+"，BookWriter："+this.writer+"，BookPrice："+this.price+"元");
    }
}
var book1=new Book("book1","Tom","59.9");
book1.sayBook();//BookName：book1，BookWriter：Tom，BookPrice：59.9元
```
在这个例子中，实例属性是在构造函数中定义的，而所有实例所共享的constructor和sayBook()方法是在原型中定义的。

注：constructor属性，每个JavaScript函数都拥有一个prototype属性，这个属性的值是一个对象，这个对象包含唯一一个不可枚举的属性constructor,对象继承的constructor指代它们的构造函数。




