# UI-Router 学习  
[来源](http://bubkoo.com/2014/01/02/angular/ui-router/guide/index/)  
## 管理状态  
**只关注状态**  
* 一个状态对应一个页面位置  
* 定义controller, template 和view等属性定义指定位置用户界面和界面行为  
* 嵌套解决页面中重复出现的位置  

### 最简单形式  
模板位置指定:  
```  
<!-- in index.html -->
<body ng-controller="MainCtrl">
    <section ui-view></section>
</body>  
```  
```  
// in app-states.js (or whatever you want to name it)
$stateProvider.state('contacts', {
  template: '<h1>My Contacts</h1>'
})  
```  
1. 模板插入位置  
状态被激活时模板自动插入负状态对应模板中包含ui-view属性的元素内部    
顶层状态, 父模板为index.html  

2. 激活状态  
    1. $state.go()  
    2. ui-sref  
    3. 导航到与状态相关联的url  
    
### templates 模板  
配置状态的模板:  
1. 配置template属性:  
```
$stateProvider.state('contacts', {
  template: '<h1>My Contacts</h1>'
})  
```  
2. 配置templateUrl属性:  
```  
$stateProvider.state('contacts', {
  templateUrl: 'contacts.html'
})  
```  
**templateUrl的值也可以是一个函数返回的url, 函数带预设参数stateParams**  
```  
$stateProvider.state('contacts', {
  templateUrl: function (stateParams){
    return '/partials/contacts.' + stateParams.filterBy + '.html';
  }
})  
```  
3. 通过templateProvider函数返回:  
```  
$stateProvider.state('contacts', {
  templateProvider: function ($timeout, $stateParams) {
    return $timeout(function () {
      return '<h1>' + $stateParams.contactId + '</h1>'
    }, 100);
  }
})  
```  
**如果想在状态被激活前让ui-view有一些默认内容, 当状态被激活之后默认内容将被状态对应模板替代  
```  
<body>
    <ui-view>
        <i>Some content will load here!</i>
    </ui-view>
</body>  
```  

### Controller控制器  
可以为模板定义控制器, 控制器不会被实例化如果模板没有定义  
设置控制器:  
```  
$stateProvider.state('contacts', {
  template: '<h1>{{title}}</h1>',
  controller: function($scope){
    $scope.title = 'My Contacts';
  }
})  
```  
如果在模块中定义了控制器, 只需制定控制器名称即可:  
```  
$stateProvider.state('contacts', {
  template: ...,
  controller: 'ContactsCtrl'
})  
```  
controllerAs:  
```  
$stateProvider.state('contacts', {
  template: ...,
  controller: 'ContactsCtrl as contact'
})  
```  
使用controllerProvider**动态**返回一个控制器函数或字符串:  
```  
$stateProvider.state('contacts', {
  template: ...,
  controllerProvider: function($stateParams) {
      var ctrlName = $stateParams.type + "Controller";
      return ctrlName;
  }
})  
```  
控制器可以使用$scope.on()方法监听事件状态转换  
**控制器可以根据需要实例化, 当相应的scope被创建时**  

### 解决器Resolve  
可以使用resolve为控制器提供可选的依赖注入项  
resolve配置是由一个key/value构成的对象:  
* key -- {string} : 注入控制器的依赖名称    
* factory -- {string:function} :  
    * string: 一个服务的别名  
    * function: 函数的返回值将作为依赖注入项, 如果函数是一个耗时操作, 那么控制器必须等待函数执行完成后才会被实例化  
例子:  
在controller实例化之前, resolve中的每一个对象都必须be resolved  
```  
$stateProvider.state('myState', {
      resolve:{

         // Example using function with simple return value.
         // Since it's not a promise, it resolves immediately.
         simpleObj:  function(){
            return {value: 'simple!'};
         },

         // Example using function with returned promise.
         // 这是一种典型使用方式
         // 请给函数注入任何想要的服务依赖，例如 $http
         promiseObj:  function($http){
            // $http returns a promise for the url data
            return $http({method: 'GET', url: '/someUrl'});
         },
         // Another promise example. 
         // 如果想对返回结果进行处理， 可以使用 .then 方法
         // 这是另一种典型使用方式
         promiseObj2:  function($http){
            return $http({method: 'GET', url: '/someUrl'})
               .then (function (data) {
                   return doSomeStuffFirst(data);
               });
         },        

         // 使用服务名的例子，这将在模块中查找名称为 'translations' 的服务，并返回该服务 
         // Note: The service could return a promise and
         // it would work just like the example above
         translations: "translations",

         // 将服务模块作为解决函数的依赖项，通过参数传入
         // 提示：依赖项 $stateParams 代表 url 中的参数
         translations2: function(translations, $stateParams){
             // Assume that getLang is a service method
             // that uses $http to fetch some translations.
             // Also assume our url was "/:lang/home".
             translations.getLang($stateParams.lang);
         },

         // Example showing returning of custom made promise
         greeting: function($q, $timeout){
             var deferred = $q.defer();
             $timeout(function() {
                 deferred.resolve('Hello!');
             }, 1000);
             return deferred.promise;
         }
      },

      // 控制器将等待上面的解决项都被解决后才被实例化
      controller: function($scope, simpleObj, promiseObj, promiseObj2, translations, translations2, greeting){
          
          $scope.simple = simpleObj.value;

          // 这里可以放心使用 promiseObj 中的对象
          $scope.items = promiseObj.items;
          $scope.items = promiseObj2.items;

          $scope.title = translations.getLang("english").title;
          $scope.title = translations2.title;

          $scope.greeting = greeting;
      }
   })  
```  

### 为$state对象提供custom数据  
使用data属性  
```  
var contacts = { 
    name: 'contacts',
    templateUrl: 'contacts.html',
    data: {
        customData1: 5,
        customData2: "blue"
    }  
}
$stateProvider
  .state(contacts)
  .state('contacts.list', {
    templateUrl: 'contacts.list.html',
    data: {
        customData1: 44,
        customData2: "red"
    } 
  })  
```  
使用:  
```  
function Ctrl($state){
    console.log($state.current.data.customData1) // 输出 5;
    console.log($state.current.data.customData2) // 输出 "blue";
}  
```  

### onEnter 和 onExit回调函数  
状态活跃/不活跃时触发:  
```  
$stateProvider.state("contacts", {
  template: '<h1>{{title}}</h1>',
  resolve: { title: 'My Contacts' },
  controller: function($scope, title){
    $scope.title = 'My Contacts';
  },
  // title 是解决依赖项，这里也是可以使用解决依赖项的
  onEnter: function(title){ 
    if(title){ ... do something ... }
  },
  // title 是解决依赖项，这里也是可以使用解决依赖项的
  onExit: function(title){
    if(title){ ... do something ... }
  }
})  
```  

### stateChangeEvents状态改变事件  
**所有上述事件在rootScope作用域触发**  
1. $stateChangeStart   
模板开始解析之前触发  
```  
$rootScope.$on('$stateChangeStart', function(event, toState, toParams, fromState, fromParams) 
    { ... });  
```
使用event.preventDefault()可以阻止模板解析发生  
```  
$rootScope.$on('$stateChangeStart', 
function(event, toState, toParams, fromState, fromParams){ 
    event.preventDefault(); 
    // transitionTo() promise will be rejected with 
    // a 'transition prevented' error
})  
```  
2. stateNotFound  
在transition时通过域名查找状态, 当状态无法找到时发生.  
该事件在scope链上广播, 只允许一次处理错误的机会.  
unfoundState将作为参数传入事件监听函数.  
```  
$state.go("lazy.state", {a:1, b:2}, {inherit:false});  
// somewhere, assume lazy.state has not been defined  
$scope.$on('$stateNotFound',  
function(event, unfoundState, fromState, fromParams){   
    console.log(unfoundState.to); // "lazy.state"  
    console.log(unfoundState.toParams); // {a:1, b:2}  
    console.log(unfoundState.options); // {inherit:false} + default options  
})  
// somewhere else  
```  
3. stateChangeSuccess  
当模板解析完成后触发  
```  
$rootScope.$on('$stateChangeSuccess',  
function(event, toState, toParams, fromState, fromParams){ ... })  
```  
4. stateChangeError  
若模板解析过程中出现错误触发  
```  
$rootScope.$on('$stateChangeError',   
function(event, toState, toParams, fromState, fromParams, error){ ... })  
```  

### View Load Events视图加载  
1. $viewContentLoading  
当视图开始加载, Dom渲染完成之前触发  
在$scope链上广播此事件  
```  
$scope.$on('$viewContentLoading',  
function(event, viewConfig){  
    // Access to all the view config properties.  
    // and one special property 'targetView'  
    // viewConfig.targetView   
});  
```  
2. $viewContentLoaded  
视图加载完成, Dom渲染完成后除法, 视图所在$scope发出该事件  
```  
$scope.$on('$viewContentLoading',   
$scope.$on('$viewContentLoaded',   
function(event){ ... });  
```  

## 状态嵌套和视图嵌套  

### 状态嵌套方法  
1. 点标记法  
```  
$stateProvider  
  .state('contacts', {})  
  .state('contacts.list', {});  // point
```  
2. 使用parent属性指定一个父状态的名称字符串, parent:'contacts'  
```  
$stateProvider  
  .state('contacts', {})  
  .state('list', {  
    parent: 'contacts'  // parent string
  });  
```  
基于对象的状态:  
```  
var contacts = {   
    name: 'contacts',  //mandatory  
    templateUrl: 'contacts.html'  
}  
var contactsList = {   
    name: 'list',      //mandatory  
    parent: contacts,  //mandatory  
    templateUrl: 'contacts.list.html'  
}  
$stateProvider  
  .state(contacts)  
  .state(contactsList)  
```  
3. 使用parent属性指定一个父状态对象, parent: contacts  

**在方法调用和属性比较时可以直接引用状态对象**  
```  
$state.transitionTo(states.contacts);  
$state.current === states.contacts;  
$state.includes(states.contacts)    
```

### 注册状态的顺序  
可以以**任何顺序**和**跨模块**注册状态  
可以**在父状态存在之前**注册子状态一旦父状态被注册,将触发自动排序,然后注册子状态  

### 状态命名  
状态**不允许重名**:  
1. 点标记法: parent属性被推测出来但并不会改变状态的名字  
2. 不使用点标记法: parent属性必须明确指定但不能让任何两个状态有相同的名称  

### 嵌套状态和视图  
当应用程序在一个特定的状态 - 当一个状态是活动状态时 - 其所有的父状态都将成为活跃状态  
```  
$stateProvider  
  .state('contacts', {  
    templateUrl: 'contacts.html',  
    controller: function($scope){  
      $scope.contacts = [{ name: 'Alice' }, { name: 'Bob' }];  
    }
  })  
  .state('contacts.list', {  
    templateUrl: 'contacts.list.html'  
  });  
  
function MainCtrl($state){  
  $state.transitionTo('contacts.list');  
}  
...  
<!-- index.html -->  
<body ng-controller="MainCtrl">  
  <div ui-view></div>  
</body>  
...  
<!-- contacts.html -->    
<h1>My Contacts</h1>  
<div ui-view></div>  
...  
<!-- contacts.list.html -->  
<ul>  
  <li ng-repeat="contact in contacts">  
    <a>{{contact.name}}</a>  
  </li>  
</ul>  
```  
1. 子状态将从父状态继承哪些属性  
    * 通过解决器解决的依赖注入项  
    > 子状态将从父状态继承通过解决器解决的依赖注入项,并且可以重写(overwrite)依赖项,可以将解决依赖项注入子状态的控制器和解决函数中
    > ```  
    > $stateProvider.state('parent', {  
    > resolve:{  
    >    resA:  function(){  
    >       return {'value': 'A'};  
    >    }  
    > },  
    > controller: function($scope, resA){  
    >     $scope.resA = resA.value;  
    > }  
    > }).state('parent.child', {  
    > resolve:{  
    >    // 将父状态的解决依赖项注入到子状态的解决函数中  
    >    resB: function(resA){  
    >       return {'value': resA.value + 'B'};  
    >    }  
    > },  
    > // 将父状态的解决依赖项注入到子状态的控制器中  
    > controller: function($scope, resA, resB){  
    >     $scope.resA2 = resA.value;  
    >     $scope.resB = resB.value;  
    > }  
    > ```  

    * 自定义的data属性  
    > 子状态将从父状态继承自定义data属性值,并且可以重写(overwrite)data属性值  
    > ```  
    > $stateProvider.state('parent', {
    > data:{
    >    customData1:  "Hello",
    >    customData2:  "World!"
    > }}).state('parent.child', {
    > data:{
    >    // customData1 inherited from 'parent'
    >    // 覆盖了 customData2 的值
    >    customData2:  "UI-Router!"
    > }});
    > $rootScope.$on('$stateChangeStart', function(event, toState){ 
    > var greeting = toState.data.customData1 + " " + toState.data.customData2;
    > console.log(greeting);
    > // 'parent'被激活时，输出 "Hello World!"
    > // 'parent.child'被激活时，输出 "Hello UI-Router!"
    > })
    > ```  

### Abstract States 抽象状态  
一个抽象的状态可以有子状态但不能显式激活,当其子状态被激活时它将被隐性激活  
使用情况:  
> * 为所有子状态预提供一个基url  
> * 在父状态中设置template属性,子状态对应的模板将插入到父状态模板中的ui-view(s)中  
> * 通过resolve属性,为所有子状态提供解决依赖项  
> * 通过data属性,为所有子状态或者事件监听函数提供自定义数据  
> * 运行onEnter或onExit函数,这些函数可能在以某种方式修改应用程序  
> * 上面场景的任意组合  

**抽象的状态模板仍然需要ui-view,来让自己的子状态模板插入其中.因此,如果您使用抽象状态只是为了预提供基url、提供解决依赖项或者自定义data、运行onEnter/Exit函数，你仍然需要设置template:"ui-view"**
```  
$stateProvider  
    .state('contacts', {  
        abstract: true,  
        url: '/contacts',  
        // Note: abstract still needs a ui-view for its children to populate.  
        // You can simply add it inline here.  
        template: '<ui-view/>'  
    }).state('contacts.list', {  
        url: '/list'  
        // url will become '/contacts/list'  
        //...more  
    }).state('contacts.detail', {  
        url: '/detail',  
        // url will become '/contacts/detail'  
        //...more  
    })  
...
$stateProvider  
    .state('contacts', {  
        abstract: true,  
        templateURL: 'contacts.html'  
    ).state('contacts.list', {  
        // loaded into ui-view of parent's template  
        templateUrl: 'contacts.list.html'  
    }).state('contacts.detail', {  
        // loaded into ui-view of parent's template  
        templateUrl: 'contacts.detail.html'  
    })  
...  
<!-- contacts.html -->  
<h1>Contacts Page</h1>  
<div ui-view></div>  
```  

## 多个视图的命名  
**可以给ui-view指定名称,这样一个模板中就可以有多个ui-view**  
**使用多个视图时,用到state的views属性(Object)**  

### 设置views属性将覆盖被覆盖的template属性  
**如果在状态中定义了views属性,那么状态中的templateUrl、template和templateProvider属性将被忽略**  
```  
<!-- index.html -->  
<body>  
  <div ui-view="filters"></div>  
  <div ui-view="tabledata"></div>  
  <div ui-view="graph"></div>  
</body>  
```  
```  
$stateProvider
  .state('report',{  
    views: {  
      'filters': {  
        templateUrl: 'report-filters.html',  
        controller: function($scope){ ... controller stuff just for filters view ... }  
      },  
      'tabledata': {  
        templateUrl: 'report-table.html',  
        controller: function($scope){ ... controller stuff just for tabledata view ... }  
      },  
      'graph': {  
        templateUrl: 'report-graph.html',  
        controller: function($scope){ ... controller stuff just for graph view ... }  
      },  
    }  
  })  
``  
每个views中的view都可以拥有自身的模板属性和控制器属性  

### 视图名称 - 相对命名和决定命名  
视图名称中有@ -- 绝对命名 -- 相对哪个state的模板  
相对命名 -- 相对父模板的ui-view  
在 ui-router 内部,views属性中的每个视图都被按照viewname@statename的方式分配为绝对名称  
> viewname是目标模板中的ui-view对应的名称  
> statename是状态的名称  
> 状态名称对应于一个目标模板  

@前面部分为空表示未命名的ui-view，@后面为空则表示相对于根模板,通常是 index.html  
```  
.state('report',{  
  views: {  
    'filters@': { },  
    'tabledata@': { },  
    'graph@': { }  
  }  
})  
```    
**绝对命名的方式可以让我们完成一些强大的功能**:  
```  
<!-- index.html (root unnamed template) -->  
<body ng-app>  
<div ui-view></div> <!-- contacts.html plugs in here -->  
<div ui-view="status"></div>  
</body>  
```  
<!-- contacts.html -->  
<h1>My Contacts</h1>  
<div ui-view></div>  
<div ui-view="detail"></div>  
```  
```  
<!-- contacts.detail.html -->  
<h1>Contacts Details</h1>  
<div ui-view="info"></div>  
```  
```  
$stateProvider  
  .state('contacts', {  
    // 根状态，对应的父模板则是index.html  
    // 所以 contacts.html 将被加载到 index.html 中未命名的 ui-view 中  
    templateUrl: 'contacts.html'   
  })
  .state('contacts.detail', {  
    views: {  
        // 嵌套状态，对应的父模板是 contacts.html  
        // 相对命名  
        // contacts.html 中 <div ui-view='detail'/> 将对应下面  
        "detail" : { },   
        // 相对命名  
        // 对应 contacts.html 中的未命名 ui-view <div ui-view/>  
        "" : { },   
        // 绝对命名  
        // 对应 contacts.detail.html 中 <div ui-view='info'/>  
        "info@contacts.detail" : { }  
        // 绝对命名  
        // 对应 contacts.html 中 <div ui-view='detail'/>  
        "detail@contacts" : { }  
        // 绝对命名  
        // 对应 contacts.html 中的未命名 ui-view <div ui-view/>  
        "@contacts" : { }  
        // 绝对命名  
        // 对应 index.html 中 <div ui-view='status'/>   
        "status@" : { }  
        // 绝对命名  
        // 对应 index.html 中 <div ui-view/>  
        "@" : { }   
  });  
```  
**不仅仅可以在同一状态设置多个视图，而且祖先状态可以由开发者自由控制**  

## 路由控制  
**在你的应用中大多数状态都有与其相关联的 url,路由控制不是设计完成 state 之后的事后想法,而是开始开发时就应该考虑的问题**  
设置一个基本url  
```  
$stateProvider  
    .state('contacts', {  
        url: "/contacts",  
        templateUrl: 'contacts.html'  
    })  
```  
> 当我们访问index.html/contacts时,'contacts'状态将被激活,同时index.html中的ui-view将被'contacts.html'填充.
> 或者,通过transitionTo('contacts')方法将状态转变到'contacts'状态,同时 url 将更新为index.html/contacts  

### URL参数  
1. 基本参数  
通常,url动态部分被称为参数,有几个选项用于指定参数.基本参数如下:  
```  
$stateProvider  
    .state('contacts.detail', {  
        // 这里设置了url参数  
        url: "/contacts/:contactId",  
        templateUrl: 'contacts.detail.html',  
        controller: function ($stateParams) {  
            // If we got here from a url of /contacts/42  
            expect($stateParams).toBe({contactId: 42});  
        }  
    })  
```  
使用花括号指定:  
```  
// 与前面的设置方法等效  
url: "/contacts/{contactId}"  
```  
2. 含正则表达式的参数  
使用花括号的方式可以设置一个正则表达式规则的参数:  
```  
// 只会匹配 contactId 为1到8位的数字  
url: "/contacts/{contactId:[0-9]{1,8}}"  
```  
**不要把捕获圆括号写进正则表达式,ui-router 的 UrlMatcher 将为整个正则表达式添加捕获**  
3. Query Parameters  
可以通过?来指定参数作为查询参数  
```  
url: "/contacts?myParam"  
// 匹配 "/contacts?myParam=value"  
```
查询多个参数:  
```  
url: "/contacts?myParam1&myParam2"  
// 匹配 "/contacts?myParam1=value1&myParam2=wowcool"  
```  

### 嵌套状态的路由控制  
1. default方式  
子状态的 url 附加到父状态的 url 之后:  
```  
$stateProvider  
  .state('contacts', {  
     url: '/contacts',  
  })  
  .state('contacts.list', {  
     url: '/list',  
  });  
```  
2. 绝对路由  
如果你使用绝对 url 匹配的方式，那么你需要给你的url字符串加上特殊符号"^":  
```  
$stateProvider  
  .state('contacts', {  
     url: '/contacts',  
  })  
  .state('contacts.list', {  
     url: '^/list',  
  });  
```  

### $stateParams 服务  
$stateParams服务是一个对象,包含 url 中每个参数的键/值.$stateParams可以为控制器或者服务提供 url 的各个部分  
**$stateParams服务必须与一个控制器相关，并且$stateParams中的“键/值”也必须事先在那个控制器的url属性中有定义**  
```  
// 如果状态中 url 属性是:  
url: '/users/:id/details/{type}/{repeat:[0-9]+}?from&to'  
// 当浏览  
'/users/123/details//0'
// $stateParams 对象将是  
{ id:'123', type:'', repeat:'0' }  
// 当浏览  
'/users/123/details/default/0?from=there&to=here'  
// $stateParams 对象将是  
{ id:'123', type:'default', repeat:'0', from:'there', to:'here' }  
```  
**$stateParams的陷阱**:  
> * 只有当状态被激活并且状态的所有依赖项都被注入时,$stateParams对象才存在.
> 这代表你不能在状态的resolve函数中使用$stateParams对象,可以使用$state.current.params来代替  
> ```  
> $stateProvider.state('contacts.detail', {  
>    resolve: {  
>     someResolve: function($state){  
>         //*** 不能在这里使用 $stateParams , the service is not ready ***//  
>         //*** 使用 $state.current.params 来代替 ***//  
>         return $state.current.params.contactId + "!"  
>      };  
>   },  
> })  
> ```  

> * 在状态控制器中,$stateParams对象只包含那些在状态中定义的参数  
> 你不能访问在其他状态或者祖先状态中定义的参数  
> ```  
> $stateProvider
> .state('contacts.detail', {  
>    url: '/contacts/:contactId',   
>    controller: function($stateParams){  
>       $stateParams.contactId  //*** Exists! ***//  
>    }
> }).state('contacts.detail.subitem', {  
>    url: '/item/:itemId',   
>    controller: function($stateParams){  
>       $stateParams.contactId //*** 注意! DOESN'T EXIST!! ***//  
>       $stateParams.itemId //*** Exists! ***//    
>    }  
> })  
> ```  

### $urlRouterProvider  
$urlRouterProvider负责处理在状态配置中指定的url路由方式之外的 url 请求的路由方式.  
$urlRouterProvider负责监视$location,当$location改变后,$urlRouterProvider将从一个列表,一个接一个查找匹配项,直到找到.  
所有 url 都编译成一个UrlMatcher对象.  
$urlRouterProvider有一些实用的方法，可以在module.config中直接使用:  
1. when() for redirection 重定向  
参数：  
what: String | RegExp | UrlMatcher -- 你想重定向的传入路径  
handler: String | Function -- 将要重定向到的路径  
    * handler 作为 String  
    如果handler是字符串,它被视为一个重定向,并且根据匹配语法决定重定向的地址  
    ```  
    app.config(function($urlRouterProvider){  
        // when there is an empty route, redirect to /index     
        $urlRouterProvider.when('', '/index');  
        // You can also use regex for the match parameter  
        $urlRouterProvider.when('/aspx/i', '/index');  
    })  
    ```  
    
    * handler 作为 Function  
    如果handler是一个函数,这个函数是注入一些服务的.如果$location匹配成功,函数将被调用.你可以选择性注入$match  
    函数可以返回:
        * falsy 表明规则不匹配,$urlRouter将试图寻找另一个匹配  
        * String 该字符串作为重定向地址并且作为参数传递给$location.url()  
        * nothing或者任何为真的值,告诉$urlRouterurl 已经被处理  
    ```  
    $urlRouterProvider.when(state.url, ['$match', '$stateParams', function ($match, $stateParams) {  
        if ($state.$current.navigable != state || !equalForKeys($match, $stateParams)) {  
            $state.transitionTo(state, $match, false);  
        }  
    }]);  
    ```  
2. otherwise() 无效路由  
参数:  
* path String | Function 你想重定向url路径或者一个函数返回url路径.函数可以包含$injector和$location两个参数.  
```  
app.config(function($urlRouterProvider){  
    // 在配置（状态配置和when()方法）中没有找到url的任何匹配  
    // otherwise will take care of routing the user to the specified url  
    $urlRouterProvider.otherwise('/index');  
    // Example of using function rule as param  
    $urlRouterProvider.otherwise(function($injector, $location){  
        ... some advanced code...  
    });  
})  
```  

3. rule() 自定义url处理  
参数:  
* handler Function 一个函数,包含$injector和$location两个服务作为参数,函数负责返回一个有效的路径的字符串.  
```  
app.config(function($urlRouterProvider){  
    // Here's an example of how you might allow case insensitive urls  
    $urlRouterProvider.rule(function ($injector, $location) {  
        var path = $location.path(), normalized = path.toLowerCase();  
        if (path != normalized) return normalized;  
    });  
})  
```  

### $urlMatcherFactory 和 UrlMatchers  
定义了url模式和参数占位符的语法.
$urlMatcherFactory是在幕后被$urlRouterProvider调用来缓存编译后的UrlMatcher对象,而不必在每次 location 改变后重新解析url.  
大多数用户不需要直接使用$urlMatcherFactory方法,但是在状态配置中非常实用,可以使用$urlMatcherFactory方法来生成一个UrlMatcher对象,并在状态配置中使用该对象  
```  
var urlMatcher = $urlMatcherFactory.compile("/home/:id?param1");  
$stateProvider.state('myState', {  
    url: urlMatcher   
});  
```  

## 组件  
1. $state / $stateProvider  
管理状态定义、当前状态和状态转换.  
包含触发状态转换的事件和回调函数,异步解决目标状态的任何依赖项,更新$location到当前状态.  
由于状态包含关联的 url,通过$urlRouterProvider生成一个路由规则来执行转换的状态.  

2. ui-view指示器  
渲染状态中定义的视图,是状态中定义的视图的一个占位符.  

3. $urlRouter / $urlRouterProvider  
管理了一套路由规则列表来处理当$location发生变化时如何跳转.  
最低级的方式是,规则可以是任意函数,来检查$location,并在处理完成时候返回true.  
支持正则表达式规则和通过$urlMatcherFactory编译的UrlMatcher对象的 url 占位符规则.  

4. $urlMatcherFactory  
将 url和占位符编译为UrlMatcher对象.  
除了$routeProvider支持的占位符语法之外,它还支持扩展语法,允许一个正则表达式指定占位符,并且能够提取命名参数和查询url的一部分.  

5. $templateFactory  
通过$http / $templateCache来加载模板,供状态配置中使用.  
