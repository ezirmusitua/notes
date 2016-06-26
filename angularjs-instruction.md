1. ng-app 指令定义一个 AngularJS 应用程序  
    * 定义了 AngularJS 应用程序的 根元素  
    * 在网页加载完毕时会自动引导(自动初始化)应用程序  

2. ng-model  
    * 将输入域的值与 AngularJS 创建的变量绑定  
    * 指令把元素值(比如输入域的值)绑定到应用程序  
    * 为应用程序数据提供类型验证  
    * 为应用程序数据提供状态  
    * 为 HTML 元素提供 CSS 类  
    * 绑定 HTML 元素到 HTML 表单..

3. ng-bind 指令把应用程序数据绑定到 HTML 视图  

4. ng-init 指令初始化 AngularJS 应用程序变量  

5. AngularJS 表达式:  
    * {{ expression }}  
    * 表达式把数据绑定到 HTML  
    * 将在表达式书写的位置"输出"数据  
    * 可以包含文字、运算符和变量  

6. Angular Module --  ng-app  
    * 定义了 AngularJS 应用  
    * 是应用程序中不同部分的容器  
    * 是应用控制器的容器  
    * 控制器通常属于一个模块  
    * 创建模块:  
    ```  
    <div ng-app="myApp">...</div>  
    <script>  
    var app = angular.module("myApp", []);  
    </script>  
    ```  
    * **函数会影响到全局命名空间**  
    * **什么时候载入库**:  
    对于 HTML 应用程序,通常建议把所有的脚本都放置在 <body> 元素的最底部
    这会提高网页加载速度,因为 HTML 加载不受制于脚本加载
    在我们的多个 AngularJS 实例中,您将看到 AngularJS 库是在文档的 <head> 区域被加载
    在我们的实例中，AngularJS 在 <head> 元素中被加载,因为对 angular.module 的调用只能在库加载完成后才能进行
    另一个解决方案是在 <body> 元素中加载 AngularJS 库,但是必须放置在您的 AngularJS 脚本前面

7. 控制器--Controller--ng-controller  
    * 用于控制 AngularJS 应用  
    * 控制器是 JavaScript 对象,由标准的 JavaScript 对象的构造函数创建  
    * 加载外部文件中的控制器:<\script src="personController.js"\>\<\/script\>  

8. AngularJS 指令  
    * 新属性,扩展 HTML  
    * 内置的指令来为应用添加功能  
    * 自定义指令  

9. ng-repeat  
重复一个 HTML 元素  

10. 创建自定义的指令  
```  
<body ng-app="myApp">  
<runoob-directive></runoob-directive>  
<script>  
var app = angular.module("myApp", []);  
app.directive("runoobDirective", function() {  
    return {  
        template : "<h1>自定义指令!</h1>"  
    };  
});  
</script>  
</body>  
```  
你可以通过以下方式来调用指令:  
元素名  
属性  
类名  
注释   
restrict限制使用方式:  
E 只限元素名使用  
A 只限属性使用  
C 只限类名使用  
M 只限注释使用  
```  
var app = angular.module("myApp", []);  
app.directive("runoobDirective", function() {  
    return {  
        restrict : "A",  
        template : "<h1>自定义指令!</h1>"  
    };  
});  
```  
11. Scope  
scope 是模型,是一个 JavaScript 对象,带有属性和方法,这些属性和方法可以在视图和控制器中使用  
Scope(作用域) 是应用在 HTML (视图) 和 JavaScript (控制器)之间的纽带  
作用范围:  
根作用域:rootScope -- 作用于整个应用中 -- 是各个 controller 中 scope 的桥梁 -- 用 rootscope 定义的值,可以在各个 controller 中使用  
使用:  
将 $scope 对象当作一个参数传递  
**视图中,你不需要添加 $scope 前缀,只需要添加属性名即可**  

12. AngularJS过滤器  
模型添加:{{ var | filter }}  
指令添加:  
```  
<li ng-repeat="x in names | orderBy:'country'">  
    {{ x.name + ', ' + x.country }}  
</li>  
```  

13. Angular Service  
服务是一个函数或对象，可在你的 AngularJS 应用中使用  
AngularJS中的服务其实就是提供一种方式抽取共用类库  
**
比如说一些工具类方法，我们传统的做法就是自己写个 utility 类，把相关的工具方法填充到utility里面去，最后把utility类放到一个全局的变量中，这样任何地方都可以调用utility的方法。
如果用AngularJS的service，你按照他的规则创建一个 utility 服务，然后在任何地方都可以通过依赖注入调用utility里面的方法
**  
常用服务:  
$http -- AngularJS 应用中最常用的服务.服务向服务器发送请求,应用响应服务器传送过来的数据  
$http.get(url) 是用于读取服务器数据的函数  
$timeout -- 对应了 JavaSript 中的 window.setTimeout 函数  
$interval -- 对应了 JavaScript 中的 window.setInterval 函数  
自定义服务:  
```  
app.service('hexafy', function() {  
    this.myFunc = function (x) {  
        return x.toString(16); 
    }  
});  
// Controller中使用  
app.controller('myCtrl', function($scope, hexafy) {  
    $scope.hex = hexafy.myFunc(255);  
});  
// Filter中使用  
app.filter('myFormat',['hexify', function(hexify) {  
    return function(x) {  
        return hexify.myFunc(x);  
    };  
}]);  
```  
14. Angular Select  
可以使用数组或对象创建一个下拉列表选项  
ng-options 创建选择框  
可以选择对象获取更多数据  
```  
<div ng-app="myApp" ng-controller="myCtrl">  
<select ng-model="selectedName" ng-options="x for x in names">  
</select>  
</div>  
<script>  
var app = angular.module('myApp', []);  
app.controller('myCtrl', function($scope) {  
    $scope.names = ["Google", "Runoob", "Taobao"];  
});  
</script>  
```  
ng-options 使用对象:  
```  
<select ng-model="selectedSite" ng-options="x for (x, y) in sites">  
</select>  
<h1>你选择的值是: {{selectedSite}}</h1>  
```  
ng-repeat 指令来下拉列表:  
选择的值是字符串,局限  
```  
<select>  
<option ng-repeat="x in names">{{x}}</option>  
</select>  
```  

15. AngularJS 表格  
```  
<div ng-app="myApp" ng-controller="customersCtrl">   
<table>  
  <tr ng-repeat="x in names | uppercase | orderBy : 'Country'">  
    <td>{{ $index + 1 }}</td>  
    <td ng-if="$odd" style="background-color:#f1f1f1">{{ x.Name }}</td>
    <td>{{ x.Name }}</td>  
    <td ng-if="$odd" style="background-color:#f1f1f1">{{ x.Country }}</td>  
    <td>{{ x.Country }}</td>  
  </tr>  
</table>  
</div>
<script>  
var app = angular.module('myApp', []);  
app.controller('customersCtrl', function($scope, $http) {  
    $http.get("http://www.runoob.com/try/angularjs/data/Customers_JSON.php")  
    .success(function (response) {$scope.names = response.records;});  
});  
</script>  
```  

16. AngularJS HTML DOM  
AngularJS 为 HTML DOM 元素的属性提供了绑定应用数据的指令  
ng-disabled 指令直接绑定应用程序数据到 HTML 的 disabled 属性  
ng-show 指令隐藏或显示一个 HTML 元素  
ng-hide 指令用于隐藏或显示 HTML 元素  

17. AngularJS 事件  
ng-click 指令定义了 AngularJS 点击事件  

18. AngularJS 表单  
输入控件的集合  
input 元素  
select 元素  
button 元素  
textarea 元素  
```  
<div ng-app="myApp" ng-controller="formCtrl">  
  <form novalidate>  
    First Name:<br>  
    <input type="text" ng-model="user.firstName"><br>  
    Last Name:<br>  
    <input type="text" ng-model="user.lastName">  
    <br><br>  
    <button ng-click="reset()">RESET</button>  
  </form>  
  <p>form = {{user}}</p>  
  <p>master = {{master}}</p>  
</div>  
<script>  
var app = angular.module('myApp', []);  
app.controller('formCtrl', function($scope) {  
    $scope.master = {firstName: "John", lastName: "Doe"};  
    $scope.reset = function() {  
        $scope.user = angular.copy($scope.master);  
    };  
    $scope.reset();  
});  
</script>  
```  

19. AngularJS 包含  
使用 ng-include 指令来包含 HTML 内容:  
```  
<body>  
<div class="container">  
  <div ng-include="'myUsers_List.htm'"></div>  
  <div ng-include="'myUsers_Form.htm'"></div>  
</div>  
</body>  
```  

20. AngularJS 动画  
AngularJS 提供了动画效果,可以配合 CSS 使用  
AngularJS 使用动画需要引入 angular-animate.min.js 库  
ngAnimate 做了什么:  
ngAnimate 模型可以添加或移除 class  
ngAnimate 模型并不能使 HTML 元素产生动画,但是 ngAnimate 会监测事件,类似隐藏显示 HTML 元素,如果事件发生 ngAnimate 就会使用预定义的 class 来设置 HTML 元素的动画.  
AngularJS 添加/移除 class 的指令:  
ng-show  
ng-hide  
ng-class  
ng-view  
ng-include  
ng-repeat  
ng-if  
ng-switch  
ng-show 和 ng-hide 指令用于添加或移除 ng-hide class 的值.  
其他指令会在进入 DOM 会添加 ng-enter 类，移除 DOM 会添加 ng-leave 属性.  
当 HTML 元素位置改变时,ng-repeat 指令同样可以添加 ng-move 类  
此外, 在动画完成后，HTML 元素的类集合将被移除  
例如: ng-hide 指令会添加一下类:  
ng-animate  
ng-hide-animate  
ng-hide-add (如果元素将被隐藏)  
ng-hide-remove (如果元素将显示)  
ng-hide-add-active (如果元素将隐藏)  
ng-hide-remove-active (如果元素将显示)  




https://o361vzvu3.qnssl.com/upload/avatar/14f1b43c90b-1afc185432223f3b?imageView/1/w/180/h/180
https://o361vzvu3.qnssl.com/upload/avatar/upload/avatar/14f1b43c90b-1afc185432223f3b?imageView/1/w/180/h/180


   // TODO: alert when quit window
            $event.onbeforeunload = function () {
                UIService.confirm('确认离开?', '您即将离开编辑页面, 正在编辑的内容将被自动保存.').then(function (yes) {
                    // TODO: add save to localstorage
                });
            }
            $scope.$on('$stateChangeStart', function () {
                UIService.confirm('确认离开?', '您即将离开编辑页面, 正在编辑的内容将被自动保存.').then(function (yes) {
                    // TODO: add save to localstorage
                });
            })