## Angular   
### ngApp  
根节点  
自动开始一个 应用  
只能有一个  
不能嵌套  
不可以和其他 directive 指令在一起使用 (ng-if, ng-inculde)  

### ngModel  
* input/select/textarea  
* validation  
* keep the state of the control  
* ng-valid/ng-invalid  
* form control  
* 默认传引用 -- Object 的 property 改变不会导致重新渲染  
* 使用 getter and setter  
> Best Practice: It's best to keep getters fast because Angular is likely to call them more frequently than other parts of your code.    

使用`ng-model-options={getterSetter: true}`  

```  
<!-- HTML -->
<div ng-controller="ExampleController">
  <form name="userForm">
    <label>Name:
      <input type="text" name="userName"
             ng-model="user.name"
             ng-model-options="{ getterSetter: true }" />
    </label>
  </form>
  <pre>user.name = <span ng-bind="user.name()"></span></pre>
</div>
// Javascript  
angular.module('getterSetterExample', [])
.controller('ExampleController', ['$scope', function($scope) {
  var _name = 'Brian';
  $scope.user = {
    name: function(newName) {
     // Note that newName can be undefined for two reasons:
     // 1. Because it is called as a getter and thus called with no arguments
     // 2. Because the property should actually be set to undefined. This happens e.g. if the
     //    input is invalid
     return arguments.length ? (_name = newName) : _name;
    }
  };
}]);
```  

### angular.Module  
*Interface for configuring angular modules.*  
* provider(name, providerType);  
provider 用于创建提供 factory / service 的实例   
Service Provider 有额外的方法可以进行配置  
* factory(name, providerFunction);  
Service Factory  
* service(name, constructor);  
Service Constructor  
* value(name, object);  
Value Service  
无法注入其他 Service  
无法注入到 Configuration 函数中  
可以被 Decorator 改写  
* constant(name, object);    
Constant Service   
无法注入其他 Service   
可以注入到 Configuration 函数中  
不可被 Decorator 改写  
* decorator(name, decorFn);   
Decorator Function  
Decorator 会*拦截* Service 的创建，重写或者修改 Service 的行为，返回值为原来的 Service 或者修改后的  
* animation(name, animationFactory);  
* filter(name, filterFactory);  
* controller(name, constructor);  
* directive(name, directiveFactory);  
* component(name, options);  
* config(configFn);  
* run(initializationFn);  

### $provider   
```  
$provider.factory / $provider.provider / $provider.service / $provider.value / $provider.constant / $provider.decorator  
```  

### angular.module  
* global  
* create/registering/retrieve angular module  

### Decorator  
一种设计模式  
如何使用  
`$porvider.decorator` / `module.decorator`  
`$delegate` -- 委派  
每个 Decorator 都能访问到 $delegate(代表的是 Service/directive/filter 的实例)  
*$provider.decorator*  
```  
angular.module('myApp', []).config([ '$provide', function($provide) {
  $provide.decorator('$log', ['$delegate', function $logDecorator($delegate) {
    var originalWarn = $delegate.warn;
    $delegate.warn = function decoratedWarn(msg) {
      msg = 'Decorated Warn: ' + msg;
       originalWarn.apply($delegate, arguments);
    };
    return $delegate;
  }]);}]);
```  
对于 $delegate 我可以:  
1. 完全替换  
2. patch it  
3. 增强功能  

针对不同的*Service*，Decorator 存在不同的规则(Service -- name, Filter/Directive -- name + 'Filter/Directive')  
|Service Type|Selector|$delegate|  
|------------|--------|---------|  
|Service     | ServiceName| Object/function|  
|Directive   | Name + 'Directive| Array.<DirectiveObject>|  
|Filter      | Name + 'Filter| Function|  

*module.decorator -- SAME AS $provider.decorator*  

### ngController  
将一个 controller attach 到 view 上  
在 angular 的 MVC 中:  
* Model -- scope  
* View -- template  
* controller -- controller class  
```  
<tag ng-controller="elements">
</tag>
```  

### ngRepeat  
根据 Collection (Array or Object) 中的对象实例化 template  
`$index` -- 第几个  
`$first`/`middle`/`$last`/`$even`/`$odd`  
*在 ngInit 中为上面的属性重命名对于嵌套 ngRepeat 很有用*  

```  
<div ng-repeat="(key, value) in myObj"> ... </div>  
```  

*ngRepeat 会检测 Collection 的变化*  
track:  
default : no duplicate  
`track by $index or trackByFunction(n)`    
针对对象:  
使用对象的 identifier track 而不是整个 Object (没有 id 就用 $index)  
*track by 总在最后*  

#### ng-repeat-start and ng-repeat-end  
Repeat 一段 html  
```  
<header ng-repeat-start="item in items">
  Header {{ item }}
</header>
<div class="body">
  Body {{ item }}
</div>
<footer ng-repeat-end>
  Footer {{ item }}
</footer>
```  

### ngClass  
根据表达式的结果动态的在 html 元素上绑定 CSS 类  
三种不同的使用方式  
1. String 表达式 -- 一个或多个空格分隔的类名称  
2. Object 表达式 -- 每个键值为`true`的`key`将作为类名称  
3. Array 表达式 -- Array 中的每个对象必须是 1 或 2  
```  
<ANY
  ng-class="expression">
...
</ANY>
<!-- Another -->
<ANY class="ng-class: expression;"> ... </ANY>
```  

### ngIf  
根据表达式的结果移除或者生成 DOM 树的一部分  

和 ng-show & ng-hide 的不同之处  
*完全移除/生成 Dom 树*而不是调整 visibility  
会生成 （原型继承自父元素) /移除元素的 Scope  
> An important implication of this is if ngModel is used within ngIf to bind to a javascript primitive defined in the parent scope. In this case any modifications made to the variable within the child scope will override (hide) the value in the parent scope.  

### ngSubmit  
只有在使用 Form 的时候可以使用  
会阻止冒泡(刷新页面等行为), 除非 Form 不包含 Contain/data-action/x-action   
*不要和 ngClick 混用导致重复提交*  

### ngClick  
ngClick 中可以接一个 expression，这代表特么的不只是只能接一个 scope.function，其实是可以接很多个的额，我特么的真是菜，，，  

### $compile.directive.Attributes  
方法:  
1. $normalize(name)  
将一个 attribute 的 name 转化为标准化的名称  
2. $addClass(classVal)  
3. $removeClass(classVal)  
4. $updateClass(newClass, oldClass)  
5. $observe(key, fn)  
*观察一个 interpolated 的属性*  
> The observer function will be invoked once during the next $digest following compilation. The observer is then invoked whenever the interpolated value changes  
6. $set(name, value)  
set Dom element attribute value  


## Angular Wiki  
### Anti Pattern  
1. Don't wrap element inside of $(). All AngularJS elements are already jq-objects  
2. Don't do if (!$scope.$$phase) $scope.$apply(), it means your $scope.$apply() isn't high enough in the call stack.  
3. Don't use jQuery to generate templates or DOM  
4. Don't create a new plugin without trying to discover, fork and pull request existing plugins first  
5. Don't use a scalar variable (null is scalar) as a model within an isolate scope (such as ng-if). (Example: http://embed.plnkr.co/qRhLfw/preview)  

### Best Practices  
1. 编写命名空间隔离的代码  
2. 对于原子事件只使用`$broadcast`/`$emit`/`$on`  
*事件是全局性的*  
*如果想只在 modules/services/widgets 中使用事件应该考虑 Services/Direvtives/Controllers/3rd Libs*  
*$scope.$watch*  
*直接通讯可以尝试注入 Service/Calling method/Directive*  
3. 尽可能使用表达式  
*ng-src/ng-href 支持 {{}}*  
*当表达式是可能改变的异步值，使用$attr.$observe(value, fn)*  
4. 使用 Directive Controller 拓展 directive  
5. 为 controller 和 directive 增加 teardown 函数  
```  
$scope.$on('destroy', ... )  
```  
6. 善用 modules  
将你的代码正确的分组能让你的 App 更加易用  
*[Demo](https://github.com/angular-app/angular-app/tree/master/client/src/app)*  
*app.controllers/services 会使你的程序不稳定*  
*app.users/app.users.edit/app.users.admin/app.projects 能让你更好的分组*  
*在不同 module 的 .config() 中分布路由*  
*Modules 能有他们各自的路由*  
*Folders 需要反映 Modules 的结构*  
*使用 NPM 和 Bower*  

### 



