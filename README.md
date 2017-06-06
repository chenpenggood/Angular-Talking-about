# Angular-Talking-about
## Angular最基本、最易提及的知识点
 - AngularJS 是一个 JavaScript 框架
 - AngularJS 扩展了 HTML
 - AngularJS 可以构建一个单一页面应用程序
-----
### 知识点
 #### 1. ng-show/ng-hide 与 ng-if的区别
  - 我们都知道ng-show/ng-hide实际上是通过display来进行隐藏和显示的。而ng-if实际上控制dom节点的增删除来实现的。因此如果我们是根据不同的条件来进行dom节点的加载的话，那么ng-if的性能好过ng-show.
-------
 #### 2. $rootScope与$scope的区别
  - 通俗的说$rootScrope 页面所有$scope的父亲。
  - 当需要参数时，它会先在自身的$scope中寻找，如果没有找到就会去$rootScrop中找。
我们来看下如何产生$rootScope和$scope吧。

step1:Angular解析ng-app然后在内存中创建$rootScope。

step2:angular回继续解析，找到{{}}表达式，并解析成变量。

step3:接着会解析带有ng-controller的div然后指向到某个controller函数。这个时候在这个controller函数变成一个$scope对象实例。
   
------
 #### 3. 表达式 {{yourModel}}是如何工作的
  - 它依赖于 $interpolation服务，在初始化页面html后，它会找到这些表达式，并且进行标记，于是每遇见一个{{}}，则会设置一个$watch。而$interpolation会返回一个带有上下文参数的函数，最后该函数执行，则算是表达式$parse到那个作用域上。
-------
 #### 4. 如何取消 $timeout, 以及停止一个$watch()
 停止 $timeout我们可以用cancel：
  ```var customTimeout = $timeout(function () {  
       // your code
    }, 1000);

   $timeout.cancel(customTimeout);
  
  ```
