## phantom API  
### 创建 phantom 对象  
`create()` will return a promise  
```  
var phantom = require('phantom');  
phantom.create(['--ignore-ssl-errors=yes', '--load-images=no']).then(...)
```  
### 打开页面  
```  
var phantom = require('phantom');  
phantom.create().then(function(ph) {  
    ph.createPage().then(function(page) {  
        // use page  
        // remember to exit phantom instance   
        ph.exit();  
    });  
});  
```  

## page API  
page 对象是一个将所有 method (和原生 phantomjs 相同)返回给 phantom 的 **proxy**, 每个 method 返回 promise  
### property  
1. 读取：  
```  
page.property('plainText').then(function(content) {  
  console.log(content);  
});  
```  
2. 设置  
```  
page.property('viewportSize', {width: 800, height: 600}).then(function() {   
    // ...   
});  
```  
3. 设置事件  
```  
page.property('onResourceRequested', function(requestData, networkRequest) {  
    console.log(requestData.url);  
});  
```  
4. trick  
**PhantomJs 不和 Node 共享内存**  
使用 property 传递变量  
```  
page.property('onResourceRequested', function(requestData, networkRequest, debug) {  
    if(debug){  
      // do something with it  
    }  
}, process.env.DEBUG);  
```  
### evalute  
```  
// return HTML of an element you can do  
page.evaluate(function() {  
    return document.getElementById('foo').innerHTML;  
}).then(function(html){  
    console.log(html);  
});   
```  
### evaluateJavaScript  
It is similar to evaluate(), but the function can't take any arguments  
```  
lmd = 'function() { return document.getElementById(\'foo\').innerHTML; }'  
page.evaluateJavaScript(lmd).then(function(html){  
    console.log(html);  
});  
```  





  