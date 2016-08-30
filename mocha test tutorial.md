## 测试脚本  
* 一个或多个 describe -- 测试套件  
* 内部一个或多个 it -- 测试用例   

## 断言库  
**代码执行结果与预期是否一致**  
`expect().to.be.[what];`  

## mocha  
```  
mocha file.test.suffix  
mocha # run test under test dir  
mocha --recursive # as same but recursive  
```  

## 通配符  
node and shell  
```  
mocha spec/{my, awesome}/.js  
mocha test/unit/*.js  
mocha 'test/**/*.@(js|jsx)'  
```  

### 命令行参数  
* --help  
* --reporter tap|...  
* mochawesome -- html display  
* --growl -- 桌面显示结果  
* --watch -- 监视脚本/自动运行  
* --bail/-b -- 未通过立即停止  
* --grep/-g -- 匹配特定  
* --invert/i -- 不符合/与 -g 一起使用  

### 配置 mocha.opts  
设置命令行参数  

### ES6 支持  
```  
npm install babel-core babel-preset-es2015 --save-dev  
```  
`.babelrc`  
```  
{
  "presets": [ "es2015" ]
}
```  
```  
../node_modules/mocha/bin/mocha --compilers js:babel-core/register
```  

### 异步测试  
`-t/--timeout`设置超时门槛  
`-s/--slow`默认显示超过75ms的测试用例  

### 测试用例钩子  
```  
before(function () {
    // 本区块所有测试用例之前执行  
});

beforeEach(function () {
    // 本区块每个测试用例之前执行  
});

after(function () {
    // 本区块所有测试用例之后执行  
});

afterEach(function () {
    // 本区块每个测试用例之后执行  
});
```  

### 测试用例管理  
`only` -- 只运行某个测试套件/测试用例  
`skip` -- 跳过指定的测试套件/测试用例  

### 浏览器测试  
mocha init dir

### 生成规格文件  
```  
mocha --recursive -R markdown > spec.md  
mocha --recursive -R doc > spec.html  
```  



