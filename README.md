# Angular-Talking-about
----
## Angular最基本、最易提及的知识点
 - AngularJS 是一个 JavaScript 框架
 - AngularJS 扩展了 HTML
 - AngularJS 可以构建一个单一页面应用程序
----
### 知识点
#### 1. ng-show/ng-hide 与 ng-if的区别
- 我们都知道ng-show/ng-hide实际上是通过display来进行隐藏和显示的。而ng-if实际上控制dom节点的增删除来实现的。因此如果我们是根据不同的条件来进行dom节点的加载的话，那么ng-if的性能好过ng-show.

#### 2. $rootScope与$scope的区别
  - 通俗的说$rootScrope 页面所有$scope的父亲。
  
  - 当需要参数时，它会先在自身的$scope中寻找，如果没有找到就会去$rootScrop中找。

  - 我们来看下如何产生$rootScope和$scope吧。
    

	step1:Angular解析ng-app然后在内存中创建$rootScope。

	step2:angular回继续解析，找到{{}}表达式，并解析成变量。

	step3:接着会解析带有ng-controller的div然后指向到某个controller函数。这个时候在这个controller函数变成一个$scope对象实例。
   

#### 3. 表达式 {{yourModel}}是如何工作的
- 它依赖于 $interpolation服务，在初始化页面html后，它会找到这些表达式，并且进行标记，于是每遇见一个{{}}，则会设置一个$watch。而$interpolation会返回一个带有上下文参数的函数，最后该函数执行，则算是表达式$parse到那个作用域上。

#### 4. 如何取消 $timeout, 以及停止一个$watch()
- 停止 $timeout我们可以用cancel：

          var customTimeout = $timeout(function () {  
       		// your code
    	}, 1000);
    
       $timeout.cancel(customTimeout);
  
- 停掉一个$watch：

	    // .$watch() 会返回一个停止注册的函数
    	function that we store to a variable  
    		var deregisterWatchFn = $rootScope.$watch(‘someGloballyAvailableProperty’, function (newVal) {  
    		  if (newVal) {
    			deregisterWatchFn();
    	  		}
    	});

#### 4. Angular Directive中restrict 中分别可以怎样设置？scope中@,=,&有什么区别
1. restrict中可以分别设置(当然你可以设置多个值比如AEC,进行多个匹配):
	- A匹配属性
	- E匹配标签
	- C匹配class
	- M 匹配注释


2. 在scope中，@,=,&在进行值绑定时分别表示:
	- @获取一个设置的字符串，它可以自己设置的也可以使用{{yourModel}}进行绑定的;
	- = 双向绑定，绑定scope上的一些属性；
	- & 用于执行父级scope上的一些表达式，常见我们设置一些需要执行的函数

			angular.module('docsIsolationExample', [
			]) 

			.controller('Controller', ['$scope', function($scope) {
		 	 	$scope.alertName = function() {
		     		 alert('directive scope &');
		  		}
			}]);
			.directive('myCustomer', function() {
		 	 return {
		    	restrict: 'E',
		    	scope: {
		     	 clickHandle: '&'
		    },
		    template: '<button ng-click="testClick()">Click Me</button>',
			    controller: function($scope) {
			      $scope.testClick = function() {
			        $scope.clickHandle();
		    	 }  
		    	}
		 	 };
			});

			-----------------
			<div ng-app="docsIsolationExample">  
			<div ng-controller="Controller">  
			  <my-customer click-handle="alertName()"></my-customer>
			</div>  
			 </div>

#### 5. 列出至少三种实现不同模块之间通信方式
- Service

- events,指定绑定的事件

- 使用 $rootScope

- controller之间直接使用$parent, $$childHead等

- directive 指定属性进行数据绑定

#### 6. 有哪些措施可以改善Angular 性能
- 使用一次绑定表达式即{{::yourModel}}

- 减少watcher数量

- 在无限滚动加载中避免使用ng-repeat,关于解决方法可以参考这篇文章

- 使用性能测试的小工具去挖掘你的angular性能问题

#### 7. factory、service 和 provider 是什么关系
- factory把 service 的方法和数据放在一个对象里，并返回这个对象

		app.factory('FooService', function(){
    	return {
	        target: 'factory',
	        sayHello: function(){
	            return 'hello ' + this.target;
       		 }
    		}
		});

- service通过构造函数方式创建 service，返回一个实例化对象

		app.service('FooService', function(){
		    var self = this;
		    this.target = 'service';
		    this.sayHello = function(){
		        return 'hello ' + self.target;
		    }
		});

- provider创建一个可通过 config 配置的 service，$get 中返回的，就是用 factory 创建 service 的内容

		app.provider('FooService', function(){
		    this.configData = 'init data';
		    this.setConfigData = function(data){
		        if(data){
		            this.configData = data;
		        }
		    }
		    this.$get = function(){
		        var self = this;
		        return {
		            target: 'provider',
		            sayHello: function(){
		                return self.configData + ' hello ' + this.target;
		            }
		        }
		    }
		});
		
		// 此处注入的是 FooService 的 provider
		app.config(function(FooServiceProvider){
		    FooServiceProvider.setConfigData('config data');
		});

	注：从底层实现上来看，service 调用了 factory，返回其实例；factory 调用了 provider，返回其 $get 中定义的内容。factory 和 service 功能类似，只不过 factory 是普通 function，可以返回任何东西（return 的都可以被访问，所以那些私有变量怎么写，你懂的）；service 是构造器，可以不返回（绑定到 this 的都可以被访问）；provider 是加强版 factory，返回一个可配置的 factory。

#### 8.angular 的数据绑定采用什么机制？详述原理
- 双向数据绑定是 AngularJS 的核心机制之一。当 view 中有任何数据变化时，会更新到 model ，当 model 中数据有变化时，view 也会同步更新，显然，这需要一个监控。

- 脏检查机制。

	原理就是：Angular 在 scope 模型上设置了一个 监听队列，用来监听数据变化并更新 view 。每次绑定一个东西到 view 上时 AngularJS 就会往 $watch 队列里插入一条 $watch，用来检测它监视的 model 里是否有变化的东西。当浏览器接收到可以被 angular context 处理的事件时，$digest 循环就会触发，遍历所有的 $watch，最后更新 dom。

		<button ng-click="val=val+1">increase 1</button>

	click 时会产生一次更新的操作（至少触发两次 $digest 循环）
	
	按下按钮

	- 浏览器接收到一个事件，进入到 angular context

	- $digest 循环开始执行，查询每个 $watch 是否变化

	- 由于监视 $scope.val 的 $watch 报告了变化，因此强制再执行一次 $digest 循环
新的 $digest 循环未检测到变化

	- 浏览器拿回控制器，更新 $scope.val 新值对应的 dom

	$digest 循环的上限是 10 次（超过 10次后抛出一个异常，防止无限循环）。

#### 9. angular 应用常用哪些路由库，各自的区别是什么？
- Angular1.x 中常用 ngRoute 和 ui.router

- 无论是 ngRoute 还是 ui.router，作为框架额外的附加功能，都必须以 模块依赖 的形式被引入。

区别：

- ngRoute 模块是 Angular 自带的路由模块，而 ui.router 模块是基于 ngRoute模块开发的第三方模块。

- ui.router 是基于 state （状态）的， ngRoute 是基于 url 的，ui.router模块具有更强大的功能，主要体现在视图的嵌套方面。

#### 10. angular 的缺点有哪些？
- 强约束----导致学习成本较高，对前端不友好，AngularJS 的约定时，生产力会很高，对 Java 程序员友好。

- 不利于----SEO因为所有内容都是动态获取并渲染生成的，搜索引擎没法爬取。

	一种解决办法是，对于正常用户的访问，服务器响应 AngularJS 应用的内容；对于搜索引擎的访问，则响应专门针对 SEO 的HTML页面。

- 对于Angular1.x ，存在 脏检查 和 模块机制 的问题。

- 性能问题----作为 MVVM 框架，因为实现了数据的双向绑定，对于大数组、复杂对象会存在性能问题。

###### 可以用来 优化 Angular 应用的性能 的办法：

1. 减少监控项（比如对不会变化的数据采用单向绑定）

2. 主动设置索引（指定 track by，简单类型默认用自身当索引，对象默认使用 $$hashKey，比如改为 track by item.id）

3. 降低渲染数据量（比如分页，或者每次取一小部分数据，根据需要再取）

4. 数据扁平化（比如对于树状结构，使用扁平化结构，构建一个 map 和树状数据，对树操作时，由于跟扁平数据同一引用，树状数据变更会同步到原始的扁平数据）
