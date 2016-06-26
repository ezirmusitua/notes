# Getting Start  
ESLint is a tool for **identifying and reporting on patterns found in ECMAScript/JavaScript code**  
with the goal of making code more **consistent and avoiding bugs**  
In many ways, it is similar to JSLint and JSHint with a few exceptions:  
* ESLint uses Espree for JavaScript parsing.  
* ESLint uses an AST to evaluate patterns in code.  
* ESLint is completely pluggable, every single rule is a plugin and you can add more at runtime.  

## Usage  
```  
$ eslint --init  
$ eslint test1.js test2.js  
```  

## Configuration  
In .eslintrc.*:  
```  
{  
    "env": {  
        "node": true,  
        "es6": true  
    },  
    "extends": "eslint:recommended",  
    "rules": {  
        "indent": [  
            "error",  
            4  
        ],  
        "linebreak-style": [  
            "error",  
            "windows"  
        ],  
        "quotes": [  
            "warn",  
            "double"  
        ],  
        "semi": [  
            "error",  
            "always"  
        ]
    }
}  
```  
**ESLint will not lint your code unless you extend from a shared configuration or explicitly turn rules on in your configuration**  
[find eslint config file](https://www.npmjs.com/search?q=eslint-config)  
[find configuration list](http://eslint.org/docs/user-guide/configuring)  


# 入门  
ESLint 主要有以下特点: 
* 默认规则包含所有 JSLint、JSHint 中存在的规则, 易迁移  
* 规则可配置性高: 可设置[warn],[error]两个 error 等级, 或者直接禁用  
* 包含代码风格检测的规则(可以丢掉 JSCS 了)  
* 支持插件扩展、自定义规则  

## 配置  
三种方式:  
* .eslintrc.*  
```  
{  
  "env": {  
    "browser": true,  
  },  
  "globals": {  
    "angular": true,  
  },  
  "rules": {  
    "camelcase": 2,  
    "curly": 2,  
    "brace-style": [2, "1tbs"],  
    "quotes": [2, "single"],  
    "semi": [2, "always"],  
    "space-in-brackets": [2, "never"],  
    "space-infix-ops": 2,  
  }  
}  
```  

* package.json 中添加 eslintConfig 配置模块  
```  
{  
  "name": "mypackage",  
  "version": "0.0.1",  
  "eslintConfig": {  
    "env": {  
      "browser": true,  
      "node": true  
    }  
  }  
}  
```  

* 直接在代码文件中定义  
```  
// 禁用 ESLint
/* eslint-disable */  
var obj = { key: 'value', }; // I don't care about IE8  
/* eslint-enable */  
// 禁用一条规则  
/*eslint-disable no-alert */  
alert('doing awful things');  
/* eslint-enable no-alert */  
// 调整规则  
/* eslint no-comma-dangle:1 */  
// Make this just a warning, not an error  
var obj = { key: 'value', }  
```  

## 工作流集成  
ESLint 可以集成到主流的编辑器和构建工具中, 以便我们在编写的代码的同时进行 lint. 
* Webstorm  
* Gulp  
```  
var gulp = require('gulp');  
var eslint = require('gulp-eslint');  
gulp.task('lint', function() {  
  return gulp.src('client/app/**/*.js')  
    .pipe(eslint())  
    .pipe(eslint.format());  
});  
```  

## 自定义规则  
在 NPM 上以 eslintplugin 为关键词, 可以搜索到很多插件:  
* eslint-plugin-react  
* ...  
如果有自行开发插件的需求: [ESLint 插件开发文档](http://eslint.org/docs/developer-guide/working-with-plugins)  
