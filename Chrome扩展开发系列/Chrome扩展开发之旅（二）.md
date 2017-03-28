# Chrome 扩展开发之旅（二）

最近比较忙，这两天终于抽时间写了我们系列的第二篇文章，大家有什么意见和建议欢迎评论

本来是想再给大家详细介绍一下chrome扩展的许多文档细节和一些定义，后来考虑到这个扩展本身的意义就是在于做出应用，过多的介绍API反而会让大家失去兴趣，故而，今天给大家带来就是一个基于chrome的一个ToDoList的纯前端小玩具

本系列课程源码都在我的github上：https://github.com/miaoihan/chrome-extensions

## 入正题
#### 项目目录
![0_1490692911678_upload-9dddde22-fcfc-4354-b088-80d8f417a03c](http://static.mafengshe.com/FgI5ZtzRSSBBLK8CJBnme1z8dzmU?imageMogr2/quality/40) 

上节课已经介绍过改文件里各个文件的作用了，这次的应用并没有多出任何的文件，都是在基础之上做的代码的扩充。

- app.js：TodoList的主文件
- background.js：后台数据通信（后来版本中用chrome.storage替换了background的数据传递）
Task.js：task类，包含task属性和方法

