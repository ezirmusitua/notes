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

### $injector  
用于获取由 provider 定义的东西，实例化类型，调用方法，load 模块  
1. get(name, [caller]);  
Return an instance of the service.  
2. invoke(fn, [self], [locals]);  
Invoke the method and supply the method arguments from the $injector.   
3. has(name);  
Allows the user to query if the particular service exists.  
4. instantiate(Type, [locals]);  
Create a new instance of JS type. The method takes a constructor function, invokes the new operator, and supplies all of the arguments to the constructor function as specified by the constructor annotation.  
5. annotate(fn, [strictDi]);  
Returns an array of service names which the function is requesting for injection. This API is used by the injector to determine which services need to be injected into the function when the function is invoked. There are three ways in which the function can be annotated with the needed dependencies.  


#### $inject Annotation  
By adding an $inject property onto a function the injection parameters can be specified.  
If a function has an $inject property and its value is an array of strings, then the strings represent names of services to be injected into the function.  
```  
// Given
var MyController = function(obfuscatedScope, obfuscatedRoute) {
  // ...
}
// Define function dependencies
MyController['$inject'] = ['$scope', '$route'];

// Then
expect(injector.annotate(MyController)).toEqual(['$scope', '$route']);
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
  }]);
}]);
```  
对于 $delegate 我可以:  
1. 完全替换  
2. patch it  
3. 增强功能  

针对不同的*Service*，Decorator 存在不同的规则(Service -- name, Filter/Directive -- name + 'Filter/Directive')  

|Service Type|Selector           |$delegate                 |    
|------------|-------------------|--------------------------|   
|Service     | ServiceName       | Object/function          |   
|Directive   | Name + 'Directive'| `Array.<DirectiveObject>`|   
|Filter      | Name + 'Filter'   | Function                 |   

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
`$first`/`$middle`/`$last`/`$even`/`$odd`  
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
<div ng-class="{true: 'active', false: 'inactive'}[isActive]">
<div ng-class {'selected': isSelected, 'car': isCar}">
<div class=”{{test}}”></div>
```  

### ngIf  
根据表达式的结果移除或者生成 DOM 树的一部分  

和 ng-show & ng-hide 的不同之处  
*完全移除/生成 Dom 树*而不是调整 visibility  
会生成 （原型继承自父元素) / 移除元素的 Scope  
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

### ngTransclude  
```  
<!-- HTML -->  
<div ng-controller="Ctrl">
      <input ng-model="title"><br>
      <textarea ng-model="text"></textarea> <br/>
      <pane title="{{title}}">{{text}}</pane>
</div>
// Javascript  
app.directive('pane', function(){
    return {
      restrict: 'E',
      transclude: true,
      scope: { title:'@' },
      template: '<div style="border: 1px solid black;">' +
                  '<div style="background-color: gray">' + 
                    '{{title}}' + 
                  '</div>' +
                  '<div ng-transclude></div>' +
                '</div>'
    };
});
```  
* {{ text }} 是在 directive tag 之间的内容  
* ng-transclude 最后会被 directive tag 中的内容代替  

高级用法:  
```  
<!-- HTML -->  
<div ng-controller="Ctrl">
      <input ng-model="title"><br>
      <textarea ng-model="text"></textarea> <br/>
      <pane title="{{title}}">
        <span class="time">{{ time }}</span>  
        <p class="type">{{ type }}</p>
        <p class="content">{{ text }}</p>
      </pane>
</div>
// Javascript  
/**
 * 是用 compile 的 transclude 参数  
 * transclueFn(scope, function (clone) { ... })  
 * scope: 作用域  
 * clone: 嵌套包含的区域, 由 jquery 封装  
**/
app.directive('pane', function(){
    return {
      restrict: 'EA',
      replace: 'true',
      transclude: true,
      scope: { title:'@' },
      template: '<div style="border: 1px solid black;">' +
                  '<div style="background-color: gray">' + 
                    '{{title}}' + 
                  '</div>' +
                  '<div ng-transclude></div>' +
                '</div>',
      compile: function (element, attrs, transcludeFn) {
          return function (scope, element, attrs) {
              transcludeFn(scope, function (clone) {
                  var title = element.find('title');
                  var time = clone.find('.time');
                  var type = clone.find('.type');
                  var text = clone.find('.content');

                  title.append(time);
                  element.append(type);
                  element.append(text);
              }
          }
      }
    };
});

/**
 * 注入并使用 $transclude  
**/
app.directive('pane', function() {
    return {
        restrict: 'EA',
        template: '<div style="border: 1px solid black;"><div class="title" style="background-olor: gray">{{title}}</div></div>',
        replace: true,
        transclude: true,
        controller: ['$scope', '$element', '$transclude', function ($scope, $element, $transclude) {
            $transclude(function(clone, scope) {
                var title= element.find('title');
                var time = clone.find('.time');
                var type = clone.find('.type');
                var text= clone.find('.content');

                title.append(time);
                element.append(type);
                element.append(text)
            });
        }],
    };
});
``` 

transclude 作用域问题:  
*会创建一个与 directive 相互独立的作用域*  





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

### 理解 scopes  
在 Angular 中:  
*子 scope 通常*原型继承*自父 scope (特例: directive 中的`scope: { ... }`)*  
*在子 scope 中对父 scope 中定义的基本数据类型变量使用双向绑定时并不会像我们想的那样工作.*  
*父 scope 中定义的基本类型变量，当我们在子 scope 中改变了值以后并不会传递到父 scope 中去*  
*解决办法，总是使用 Object (.)*  

#### JS 中的原型继承  
*原型链*  
*基本类型的更改不会进行原型链 consulted，直接在 child 处新建，shadow parent*  
*对象更改会进行原型链 consulted，找到则会修改 parent 的属性，否则 child 处新建*  
> * If we read childScope.propertyX, and childScope has propertyX, then the prototype chain is not consulted.  
> * If we set childScope.propertyX, the prototype chain is not consulted.  

#### Angular 中的作用域继承   
> * The following create new scopes, and inherit prototypically: ng-repeat, ng-include, ng-switch, ng-view, ng-controller, directive with scope: true, directive with transclude: true.  
> * The following creates a new scope which does not inherit prototypically: directive with scope: { ... }. This creates an "isolate" scope instead.  

1. ngInclude  
创建一个原型继承的作用域  
对于来自父节点的基本类型变量，可以使用 $parent.myPrimitive 进行改变 

2. ngSwith  
类似 ngInclude  

3. ngRepeat  
每次迭代都会创建新的原型继承的 scope，但是*同样会在作用于中为每个 item 的值指定新的属性(名字是 key)*    
如果 item 是基本类型对象，值的 copy 会被赋予 child scope 作用域。改变子作用域中 item 的值并不会影响到 parent scope 中 array 中 item 的值  
如果是对象还是会的  

4. ngView  
类似 ngInclude  

5. ngController  
类似 ngInclude  
两个 controller 之间不应该利用 $scope 进行信息交互 -- 使用 services  

6. directive  
* scope: false -- use parent，bad way  
* scope: true -- new inherits from parent，like ngInclude
* scope: { ... } -- new isolate(still child), best choice, =/@/& from parent  
* transclude: true -- new inherits from parent

Summary:  
> * normal prototypal scope inheritance -- ng-include, ng-switch, ng-controller, directive with scope: true  
 > * normal prototypal scope inheritance with a copy/assignment -- ng-repeat. Each iteration of ng-repeat creates a new child scope, and that new child scope always gets a new property.  
> * isolate scope -- directive with scope: {...}. This one is not prototypal, but '=', '@', and '&' provide a mechanism to access parent scope properties, via attributes.  
> * transcluded scope -- directive with transclude: true. This one is also normal prototypal scope inheritance, but it is also a sibling of any isolate scope.  


### 了解 DI -- 依赖注入  
#### $provider  
*how to create a injectable thing -- services -- created by providers -- $provider*  
*get hold of sth by asking for it to be injected into an application's config function*  
```  
// create 
myMod.config(function($provide) {
$provide.provider('greeting', function() {
  this.$get = function() {
    return function(name) {
      alert("Hello, " + name);
    };
  };
});
});
// use  
myMod.controller('MainController', function($scope, greeting) {
  $scope.onClick = function() {
    greeting('Ford Prefect');
  };
});
```  
factory, service, value and so on are just shortcut that use to define various parts of a provider.  

Same way with using config:  
```  
var myMod = angular.module('myModule', []);

myMod.provider("greeting", ...);
myMod.factory("greeting", ...);
myMod.service("greeting", ...);
myMod.value("greeting", ...);
```  

#### $injector  
The injector is responsible for actually creating instances of our services using the code we provided via $provide   
每个 Angular App start 的时候都会创建一个 $injector，通过它你可以通过将它注入到任何可以注入函数的方式使用它  

*你可以将任何被 $injector.invoke 调用的 services 注入到函数中*  
* controller definition functions  
* directive definition functions  
* filter definition functions  
* the $get methods of providers (aka the factory definition functions)  

#### 配置 Providers  
不适用 factory/value 等简单方法的原因是因为 provider 允许更多的配置  

Angular 通过两个步骤运行你的 App:  
1. Config -- 配置 provider/controllers/directives/filters module.config  
2. Run -- complie DOM and start App -- module.run  
*在 config 的时候只有 $provider (constant)可以被注入*  

#### Controllers($controller)  
只能将东西注入 controller，反之不行 -- controller 不是通过 provider 创建的  
```  
myMod.controller('MainController', function($scope) {
  // ...
});
// Actually is 
myMod.config(function($controllerProvider) {
  $controllerProvider.register('MainController', function($scope) {
    // ...
  });
});
```  
> Later, when Angular needs to create an instance of your controller, it uses the $controller service (which in turn uses the $injector to invoke your controller function so it gets its dependencies injected too).  

#### Filters($filter - $filterProvider) and Directives($directive - $compile & $compileProvider)  
SAME WAY WITH controller

#### Summary  
Any function that gets called with $injector.invoke can be injected into. This includes, but is not limited to:  
* controller  
* directive  
* factory  
* filter  
* provider $get (when defining provider as an object)  
* provider function (when defining provider as a constructor function)  
* service  

The provider creates new services that can be injected into things. This includes:  
* constant  
* factory  
* provider  
* service  
* value  

*That said, built-in services like $controller and $filter can be injected, and you can use those services to get hold of the new filters and controllers you defined with those methods (even though the things you defined aren't, by themselves, able to be injected into things).*  

*Other than that, any injector-invoked function can be injected with any provider-provided service--there is no restriction (other than the config and run differences listed herein).*  


### 理解 Directive  
创建 Directive 的基本步骤:  
```  
myApp.directive('uiJq', function InjectingFunction(){
  // === InjectingFunction === //
  // Logic is executed 0 or 1 times per app (depending on if directive is used).
  // Useful for bootstrap and global configuration
  return {
    compile: function CompilingFunction($templateElement, $templateAttributes) {
      // === CompilingFunction === //
      // Logic is executed once (1) for every instance of ui-jq in your original UNRENDERED template.
      // Scope is UNAVAILABLE as the templates are only being cached.
      // You CAN examine the DOM and cache information about what variables
      //   or expressions will be used, but you cannot yet figure out their values.
      // Angular is caching the templates, now is a good time to inject new angular templates 
      //   as children or future siblings to automatically run..

      return function LinkingFunction($scope, $linkElement, $linkAttributes) {
        // === LinkingFunction === //
        // Logic is executed once (1) for every RENDERED instance.
        // Once for each row in an ng-repeat when the row is created.
        // Note that ng-if or ng-switch may also affect if this is executed.
        // Scope IS available because controller logic has finished executing.
        // All variables and expression values can finally be determined.
        // Angular is rendering cached templates. It's too late to add templates for angular
        //  to automatically run. If you MUST inject new templates, you must $compile them manually.
      };
    }
  };
})
```  
*只可以在 LinkingFunction 的 $scope 中使用数据*  
*只可以依靠 LinkingFunction 中的 final Dom 配置*  
*无法依靠子/兄弟节点，因为他们还没有被渲染*  

#### pre/post Linking Function  
```  
// default: post linking function 
LinkingFunction: function ($scope, $element, $attributes) { ... }  
// pre/post linking function  
linkingFunction: { 
    pre: function ($scope, $element, $attributes) { ... }, // fire on parent first  
    post: function ($scope, $element, $attributes) { ... }, // fire on child first  
}  
```  

#### $element === angular.element() === jQuery() === $()  
*You do NOT have to wrap AngularJS elements in jQuery()*  

#### $attributes.$observe()  
一个带有`{{}}`，并且其中值可能会多次变化并且需要被计算出来的的节点  
1. $scope.$watch()  
2. $attributes.$observe('myOtherAttribute', function(newValue))  
2 和 1 相比:  
* 第一个参数是 attribute 的名称  
* function 中得到的是为你计算好的 newValue 的值  

*NOTE: This means that you can only access this attribute asynchronously*  
*NOTE: If you want to reliably access the attribute pre-evaluation then you should do it in the CompileFunction*  

#### 拓展第三方 Directive  
增强但不改变  
1. Global Configuration  
2. Require Directives  
```  
// <div a b></div>
ui.directive('a', function() {
  return {
    controller: function() {
      this.data = {}
      this.changeData = function( ... ) { ... }
    },
    link: function($scope, $element, $attributes, controller) {
      controller.data = { ... }
    }
  }
})
myApp.directive('b', function() {
  return {
    require: 'a',
    link: function($scope, $element, $attributes, aController) {
      aController.changeData()
      aController.data = { ... }
    }
  }
})
```  
3. Stacking Directives  
使用相同的名字，两个都会被执行  

4. ngInclude  
使用 ngInclude 或者直接在新建的 directive 中使用需要增强的 directive  

#### 特殊化 directive 配置  
根据不同的配置针对同一个 directive 使用不同的名字实例化  
```  
// <div b></div>
ui.directive('a', ... )
myApp.directive('b', function(aDirective){
   return angular.extend({}, aDirective[0], { templateUrl: 'newTemplate.html' });
})
```  


