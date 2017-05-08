> 原文链接：[Exploring Decorators in JavaScript](https://miguelmota.com/blog/exploring-decorators-in-javascript/)
>作者：Miguel Mota

在面向对象编程中，[装饰器模式](https://en.wikipedia.org/wiki/Decorator_pattern)是很受欢迎的一种设计模式。这种设计模式允许我们在运行时为对象添加、移除或者是修改行为。在ES2016/ES7的最新迭代版本中，提出了关于[JavaScript迭代器](https://github.com/wycats/javascript-decorators)的提议，允许我们在设计时诠释和修改class和属性。接下来，我将列举一些装饰器的例子，以及如何在自己的代码中使用装饰器。

## 装饰器设计模式
JavaScript的装饰器可以看作是一个函数，这个函数将目标函数的target、name以及descriptor作为它的参数。

target是指目标构造函数。例如，如果装饰器在class的构造函数中，target指向class的构造函数。如果装饰器在class的方法中，target指向这个方法。

name就是指向方法的method。class构造函数的装饰器没有name。

descriptor描述的是数据和访问器。如果你曾经使用过Object.defineProperty()，说明你使用过描述符（descriptor）。数据和访问器的描述符都共享所需的keys，configurable （可修改的）enumerable（例如 可以被for...in遍历到）。数据描述符有其他的keys，value 和 writable（重新赋值）。访问器有其他的keys：get（getter）和set（setter）。

一个使用数据descriptor来修改属性的例子：

```
Object.defineProperty(obj, 'key', {
  enumerable: false,
  configurable: false,
  writable: false,
  value: 'static'
});
```

## 实例

下面是JavaScript中装饰器的一些例子：

### 只读的装饰器

在这个例子中，装饰器使得方法只读，这意味着我们不能修改这个方法。我们要做的就是设置writable描述符属性为false。

```
function readonly(target, name, descriptor) {
  descriptor.writable = false;
  return descriptor;
}
```
只读装饰器的使用：

```
class Robot {
  @readonly
  destroyHumans() {
    return `Destroying humans.`;
  }
}

const robot = new Robot();
console.log(robot.destroyHumans());
// "Destroying humans."

robot.destroyHumans = () => { return false; };
// TypeError: Cannot assign to read only property 'destroyHumans' of [object Object]
```
现在没有办法组织robots毁灭人类了。

### 日志装饰器

装饰器一种很方便的用法就是快速为方法添加记录日志的行为。下面这个例子中，我们实现了一个日志装饰器，函数被调用时会记录函数名。这个装饰器也可以自定义日志消息。请注意，如果我们要为装饰器提供参数，我们需要返回一个函数。

```
function log(target, name, descriptor) {
  let message = `LOG: Calling \`${name}\` function.`;

  if (typeof target === 'string') {
    message = target;

    return (target, name, descriptor) => {
      console.log(`LOG: ${message}`, name);
      return descriptor;
    };
  } else {
    console.log(message);
    return descriptor;
  }
}
```
日志装饰器的使用：

```
class Robot {
  @log
  destroyHumans() {
    return `Destroying humans.`;
  }
}

const robot = new Robot();
console.log(robot.destroyHumans());
// LOG: Calling `destroyHumans` function.
// "Destroying humans."
```
日志装饰器自定义日志消息：

```
class Robot {
  @log('Invoking the function `%s`')
  destroyHumans() {
    return `Destroying humans.`;
  }
}

const robot = new Robot();
console.log(robot.destroyHumans());
// LOG: Invoking the function `destroyHumans`
// "Destroying humans."
```
当robots攻击人类的时候，现在会有日志信息。

### 时间日志装饰器

记录一个函数的执行时间也是装饰器一种很好的用法。为了实现这个目标，我们将在时间记录开始和结束调用之间调用函数，并在最后返回结果。注意我们需要保持对原始函数的引用，用包装函数重写描述符value的属性。

```
function time(target, name, descriptor) {
  const fn = descriptor.value;

  const decoratedFn = function() {
    console.time(name) ;
    const result = fn.apply(target, arguments);
    console.timeEnd(name);
    return result;
  };

  descriptor.value = decoratedFn;

  return descriptor;
}
```
时间日志装饰器的使用：

```
class Robot {
  @time
  destroyHumans() {
    let humans = 7e9;
    while (humans--) {
      this.obliterate();
    }

    return `Humans destroyed.`;
  }

  obliterate() {}
}

const robot = new Robot();
console.log(robot.destroyHumans());
// destroyHumans: 7021ms
// Humans destroyed.
```
 正如我们所能看到的，机器人需要花大约7秒能毁灭全人类。

### 装饰的装饰器

装饰器能用于包装其他方法。这里是一个简单的装饰装饰器。

```
function decorate(fn) {
  return (target, name, descriptor) => {
    return {
      configurable: true,
      enumerable: false,
      value: () => {
        return fn(descriptor.value);
      }
    }
  };
}
```
在这个例子中，我们用[memoize](https://en.wikipedia.org/wiki/Memoization)方法包装我们的方法，并且返回缓存的值，或者值被缓存：

```
function memoize(fn) {
  const cached = memoize.cache[fn];

  if (cached) {
    console.log('Cache hit!');
    return cached;
  }

  const value = fn();
  memoize.cache[fn] = value;

  console.log('Cache miss.');
  return value;
}

memoize.cache = {};

class Robot {
  @decorate(memoize)
  destroyHumans() {
    return 'Humans destroyed.';
  }
}

const robot = new Robot();
console.log(robot.destroyHumans());
// Cache miss.
// Humans destroyed.
console.log(robot.destroyHumans());
// Cache hit!
// Humans destroyed.
```
记忆化在计算量大的时候是很有用的。

### 混合装饰器
利用混合装饰器，我们可以为class添加或混合更多的行为。我将使用混合的对象，扩展class的原型；来包含对象：

```
function mixin(...mixins) {
  return (target, name, descriptor) => {
    mixins.forEach((obj) => {
      for (const key in obj) {
        const desc = Object.getOwnPropertyDescriptor(obj, key);

        Object.defineProperty(target.prototype, key, desc);
      }
    });

    return descriptor;
  };
}
```
混合装饰器的使用：

```
const BrainMixin = {
  think() {
    return 'Today is sunny.';
  }
};

const PhilosophyMixin = {
  ponder() {
    return 'What is the meaning of life?';
  }
};

@mixin(BrainMixin, PhilosophyMixin)
class Robot {
  destroyHumans() {
    return 'Humans destroyed.';
  }
}

const robot = new Robot();
console.log(robot.destroyHumans()); // "Humans destroyed."
console.log(robot.think()); // "Today is sunny."
console.log(robot.ponder()); // "What is the meaning of life?"
```
robots现在是哲学家。

## 使用装饰器

[Bebel](https://babeljs.io/)已经成为[代码转换](https://en.wikipedia.org/wiki/Source-to-source_compiler)工具。我们可以使用[glup](http://gulpjs.com/)来自动化代码转换过程。

### Babel Gulpfile
安装需要的babel和gulp依赖。由于[TC39](http://www.ecma-international.org/memento/TC39.htm)会阻止装饰器的实现，所以我们需要使用“legacy”来转换装饰器插件。

```
npm install --save-dev babel babel-register gulp gulp-babel babel-plugin-transform-decorators-legacy
```
gulpfile.js

```
require('babel-register')();
const gulp = require('gulp');
const babel = require('gulp-babel');

gulp.task('compile', () => {
  return gulp.src('examples/time.js')
    .pipe(babel({
      plugins: [
        'transform-decorators-legacy'
      ]
    }))
    .pipe(gulp.dest('dist'));
});
```
用法：

```
gulp compile
```

### Babel CLI

为了快速开始，我们可以使用[Bebel CLI](https://babeljs.io/docs/usage/cli/).我们需要安装babel以及babel装饰器插件。

```
npm install babel -g
npm install --save-dev babel-plugin-transform-decorators-legacy
```

用法：

```
babel-node --plugins transform-decorators-legacy index.js
```

## 总结

装饰器使得组合函数的功能变得容易，也用简洁的语法提高了代码可读性。想了解更多装饰器的实例，请看我的github仓库[ES7-examples](https://github.com/miguelmota/es7-examples)。









