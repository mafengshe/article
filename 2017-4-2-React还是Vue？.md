
在2016年，React巩固了它JavaScript web框架之王的地位。这一年，web和原生APP的库都迅速发展，同时与Angular相比，也具有明显优势。

与此同时，2016年对于Vue而言，也是很重要的一年。Vue2的发布给JavaScript社区带来了巨大的冲击，在Github上获得了25000个star。

不可否认，React和Vue的范围十分类似：两者都是轻量级的组件库，用于构建用户界面，专注于视图层。两者都可以用于简单项目，也可以使用工具将它们扩展为复杂的应用程序。

因此，很多web开发人员都想知道，到底应该选择哪一个？一个明显优于另一个？有什么需要注意的利弊吗？还是说，两者基本相同？

## 两个框架，两个倡导者

在本文中，我想彻底地公正地比较两者之前的区别。唯一的问题就是：我是Vue的狂热粉丝，必然会有一些偏见。我经常在我的项目中使用Vue，还发布了一些在线教程，即[Ultimate Vue.js Developers course](https://www.udemy.com/vuejs-2-essentials/?couponCode=JSDOJO-CM)

为了摆脱我偏见的影响，我带来了我的朋友Alexis Mangin，他是一名优秀的JavaScript开发者，也是React的狂热爱好者。他沉浸在React中，经常在Web和移动APP开发中使用React。

Alexis曾经问我：“为什么你这么喜欢Vue，从来不用React？”。由于我不太了解React，我很难回答这个问题。所以，我决定，一起坐下来，用笔记本展示我们选择的库能提供的东西。

![](/images/2017-3-3-reactorvue1.jpeg)

经过激烈的讨论和互相学习，我们得出了以下6个结论。

### 如果你喜欢使用模板（ 或需要一些其中的选项）构建应用程序，那么请选择Vue

将标记放在HTML文件中是Vue应用程序的默认选项。与Angular类似，大括号用于数据绑定表达式，指令（特殊的HTML属性）用于向模板添加功能。下面是一个简单的Vue例子。它打印出一条消息，有一个按钮可以动态反转消息：

```
<div id="app">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>

new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue.js!
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('');
    }
  }
});
```

相比之下，React应用程序避开模板，要求开放人员在JavaScript中创建DOM，通常用JSX辅助，下面是用React来实现同样的功能：

```
 class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      message: 'Hello React.js!'
    };
  }
  reverseMessage() {
    this.setState({ 
      message: this.state.message.split('').reverse().join('') 
    });
  }
  render() {
    return (
      <div>
        <p>{this.state.message}</p>
        <button onClick={() => this.reverseMessage()}>
          Reverse Message
        </button>
      </div>
    )
  }
}
ReactDOM.render(App, document.getElementById('app'));

```

对于学习标准的初级web开发者而言，模板更容易理解。但是也有很多经验丰富的开发人员乐意使用模板，因为模板可以更好地分离布局和功能，同时也可以选择向Pug这样的预处理器。

但是，使用模板需要学习所有的HTML扩展语法，渲染函数只需要了解标准的HTML和JavaScript

### 如果你喜欢简单的能正常工作的，那么请选择Vue

一个简单的Vue项目可以不需要解析，直接在浏览器中运行，这允许Vue可以像jQuery一样放在项目中。

虽然在技术上使用React也是可行的，但是典型的React代码更倾向于像classes和non-mulating 数组方法这样的JSX和ES6的特性。但是Vue在简洁设计方面更为深入。我们来比较两者如何处理应用程序的数据（即“状态”）。

在React中State是不可以直接改变的，需要调用setState接口：

```
this.setState({ 
    message: this.state.message.split('').reverse().join('') 
});
```

当前和之前的状态差异让React知道什么时候在DOM中重新渲染以及渲染什么，因此不可变的状态是非常必要的。

相比之下，数据可以在Vue中突变。相同的数据数据在Vue中发生变化更为简单。


```
// Note that data properties are available as properties of 
// the Vue instance
this.message = this.message.split('').reverse().join('');
```

在你得出Vue渲染系统比React渲染低效的结论之前，让我们来看看Vue中的状态管理：当你向状态添加新对象时，Vue会遍历它的所有属性并且转换为getter和setter。Vue系统会持续追踪状态并且在状态发生变化时，自动重新渲染DOM。

令人印象深刻的是，Vue中的状态改变更为简洁的同时，重新渲染系统的效率其实比React更好。

Vue的反应系统确实有值得注意的事项。例如：它不能检测到属性的添加、删除以及特定数组的变化。在这种情况下，可以使用Vue API中类似React的set方法。

### 如果你希望你的程序更小更快，那么请选择Vue

React和Vue都将构建一个虚拟DOM，并且在应用程序状态更改时同步实际的DOM。两者都有自己的优化方法。Vue核心开发人员提供了一个基准测试，展示了Vue的渲染系统比React的更快。在这个测试中，10000个项目的列表被渲染了100次。下面的表格展示了比较的结果。

![](/images/2017-3-30-react-or-vue2.png)

在vuejs.org上发布的基准

从实际角度而言，这种基准只与边缘情况有关。大多数应用程序不需要经常进行这种操作，因此不能将其视为比较的一个重要因素。

虽然页面的大小与项目相关，Vue又占据了优势。目前发布的Vue库只有25.6KB。

要在React中获得类似的功能，你需要使用React DOM（37.4KB）和React with Addons库（11.4KB），总共为48.8KB，几乎是Vue的两倍。为了公平起见，你可以使用React获得更多的API，但是不会有双倍的功能。

### 如果你想要构建大型应用程序，那么请选择React

用Vue和React来实现简单的应用程序，就像本文开头所说的一样，大部分开发者会偏向于Vue。这是以为基于模板的应用程序乍看更容易理解，并且能够更快启动。

但是这些初始的好处会导致大型程序发展的缓慢。模板很容易出现运行时错误，难以测试，也不同意重组或分解。

相比之下，JavaScript制作的模板可以被组织成可分解的组件，DRY code也更容易重用和测试。

Vue还具有组件系统和渲染功能。但是React的渲染系统配置性更高，并且具有浅渲染的特性，与React的测试程序结合时，可以提供更多可测试和可维护的代码。

同时，React不可变的代码并不简单，当透明度和可测试性至关重要时，它会发挥更大的作用。

### 如果你想要一个适用于web和原生APP的库，那么请选择React

React Native是用JavaScript创建原生APP的库。它与React相同，只是将web组件换成了原生组件。如果你学习了React.js，那么你很快就可以学会React Native，反之亦然。


```
import React, { Component } from 'react'; 
import { AppRegistry, Text, View } from 'react-native';  
class HelloWorld extends Component {   
  render() {     
    return (       
      <View>         
        <Text>Hello, React Native!</Text>
      </View>
    );   
  }
}
AppRegistry.registerComponent('HelloWorld', () => HelloWorld);
```

重要的是，开发人员可以构建一个web或者原生APP而不需要不同的知识体系和工具。如果您打算开发web和移动APP，React会给你带来巨大冲击。

阿里巴巴的Weex是另一个跨平台UI项目。目前，它认为Vue提供了灵感，并且使用了很多相同的语法，并计划完全集成Vue。但是，这个计划的时间表和细节尚未清晰。

由于Vue将HTML模板作为其设计的核心部分，也没有将客户端渲染作为当前的特性，因此很难看到原生的Vue像React.js或React Native一样紧密。

### 如果你想要最大的生态系统，那么请选择React

毫无疑问，React目前是最受欢迎的库，NPM下载量每月约2.5M，Vue每月的下载量为225k。

![](/images/2017-3-30-react-or-vue3.png)

人气带来的好处不止一点半点。这意味有更多的文章、教程和Stack Overflow回答能够帮助。也意味着在项目中有更多的工具和附加组件可用，同时节省了开发人员自己构建所有模块的时间。

两者都是开源的，但是React诞生自Facebook，受益于它。开发者和热爱React的公司都可以放心，因为React一直被维护。

相比之下，Evan You一个人创建了Vue，同时它也是Vue唯一的全职维护者，Vue得到了一些企业的赞助，但是还达不到Facebook或是Google的规模。

为了使得Vue团队更为可信，它的小体积和独立性目前并不被当做劣势。Vue有规律的发布周期，令人印象深刻的是，Vue在Github上只有54个公开的问题，3456个关闭的问题，而React有530个开放问题，3447个关闭的问题。

### 如果你已经选择了其中的一个，那么也不需要切换了

总而言之，我们发现，Vue的优势是：
- 模板或渲染的灵活选项
- 语法以及项目设置的简单
- 渲染速度更快，体积更小

React的优势是：
- 更大的规模、更多的使用者、更好得可测试性
- Web端和原生APP
- 能提供更多支持和工具的更大的生态系统

然而，React和Vue都是特殊的UI库，并且具有更多的相似之处。它们都有的优势是：
- 使用虚拟DOM快速渲染
- 轻量级
- 响应式组件
- 服务端渲染
- 轻松集成的的路由、捆绑和状态管理
- 强大的支持和社区

如果你认为我们漏掉了一些东西，欢迎在评论中说明。Happy developing！

原文链接：[https://www.codementor.io/vuejsdevelopers/react-or-vue-which-javascript-ui-library-should-you-be-using-6hri3num4](https://www.codementor.io/vuejsdevelopers/react-or-vue-which-javascript-ui-library-should-you-be-using-6hri3num4)










