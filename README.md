# Angular-Talking-about
## Angular最基本、最易提及的知识点
 - AngularJS 是一个 JavaScript 框架
 - AngularJS 扩展了 HTML
 - AngularJS 可以构建一个单一页面应用程序
-----
### 知识点
 1. ng-show/ng-hide 与 ng-if的区别
 我们都知道ng-show/ng-hide实际上是通过display来进行隐藏和显示的。而ng-if实际上控制dom节点的增删除来实现的。因此如果我们是根据不同的条件来进行dom节点的加载的话，那么ng-if的性能好过ng-show.

