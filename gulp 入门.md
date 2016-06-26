[来源](http://www.ydcss.com/archives/18)  
前端开发过程中对代码进行构建的工具,`自动化`项目的构建利器  
gulp是基于Nodejs的自动任务运行器, 她能自动化地完成 javascript/coffee/sass/less/html/image/css 等文件的的测试、检查、合并、压缩、格式化、浏览器自动刷新、部署文件生成, 并监听文件在改动后重复指定的这些步骤  
实现上,借鉴了Unix操作系统的**管道(pipe)**思想,前一级的输出,直接变成后一级的输入  

## Stream 流  
用一个形象的比喻来形容将一个文件的内容读取出来并写入另一个文件中, 可以将它想象成文件内容像是水流，从一个文件“流”入另一个文件.  
在这输入流到输出流的中间, 我们可以对"流"做一些事情  
### Gulp 是如何操纵流的  
gulp.src: 打开文件  
.pipe: 使用管道传输并且可以在管道中进行相应的修改  
gulp.dest: 将管道中内容传到相应目的地去  

## 作用:  
1. 能对网站资源进行优化  
2. 重复的任务能够使用正确的工具自动完成  

## 使用  
### 安装 gulp  
```  
# 安装 gulp  
$ npm install gulp  
# 安装插件  
$ npm install gulp-less  

## gulp 配置文件  
```  
                                               //导入工具包 require('node_modules里对应模块')  
var gulp = require('gulp'),                    //本地安装gulp所用到的地方  
    less = require('gulp-less');  
                                               //定义一个testLess任务（自定义任务名称  
gulp.task('testLess', function () {  
    gulp.src('src/less/index.less')            //该任务针对的文件  
        .pipe(less()) //该任务调用的模块  
        .pipe(gulp.dest('src/css'));           //将会在src/css下生成index.css  
});  
gulp.task('default',['testLess', 'elseTask']); // 定义默认任务  
gulp.task(name[, deps], fn)                    // 定义任务 name: 任务名称 deps: 依赖任务名称 fn: 回调函数  
gulp.src(globs[, options])                     // 执行任务处理的文件  globs：处理的文件路径(字符串或者字符串数组)   
gulp.dest(path[, options])                     // 处理完后文件生成路径    
```  

## 运行  
```  
# 默认执行全部  
$ gulp <task name>  
```  

## 代码  
```  
var gulp = require("gulp");  
var through = require("through2")  
  
gulp.task("sync1", function () {  
    console.log(" sync task 1 ");  
});  
  
gulp.task("sync2", function () {  
    console.log(" sync task 2 ");  
});  
  
gulp.task("sync3", function () {  
    console.log(" sync task 3 ");  
});  
  
gulp.task("async", function (done) {  
    console.log(" I will waiting for sync tasks done ");  
    setTimeout( function () {  
        console.log(" async task ");  
        done();  
    }, 2000);  
});  
  
gulp.task("syncs", ["async", "sync1", "sync2", "sync3"],   
    function() {  
        console.log(" syncs done ");  
    }  
);  
  
gulp.task("src-dist", function () {  
    gulp.src("./**/.html")  
    .pipe(gulp.dest("./dist"));  
});  
  
gulp.task("build-index", function () {  
    gulp.src("./index.html")  
    .pipe(through.obj(function (file, encode, cb) {  
        var contents = file.contents.toString(encode);  
          
        var HTMLMinifier = require("html-minifier").minify;  
          
        var minified = HTMLMinifier( contents, {  
            minifyCSS: true,   
            minifyJS: true,   
            collapseAttributeQuotes: true,   
            removeAttributeQuotes: true  
        });  
    }))  
    .pipe(gulp.dest("./dist"));  
});    
  
minifyAndComboJS = function (name, encode, files) {  
    var fs = require("fs");  
      
    var UglifyJS = require("uglify-js");  
      
    var content = ""  
      
    files.forEach(function (js) {  
        var minified = UglifyJS.minify(js).code;  
        content += minified;  
    });  
      
    if (content) {  
        var combo = "/static/js/" + name;  
    }  
    fs.writeFileSync(combo, content);  
      
    gulp.src(combo)  
    .pipe(gulp.dest("./dist/static/js"));  
}  
  
gulp.task("build-js-lib", function () {  
    gulp.src("./static/js/lib/**/*.js")  
    .pipe(trhough.obj(function (file, encode, cb) {  
        var UglifyJS = require(uglify-js);  
          
        var contents = file.contents.toString(encode);  
        var mionified = UglifyJS.minify(contents, {fromString: true}).code;  
          
        file.contents = new Buffer(minified, encode);  
        cb(null, file, encode);  
    }))  
    .pipe(gulp.dest("./dist/static/js/lib"));  
});  
  
function minifiyAndComboCSS(name, encode, files) {  
    var fs = require("fs");  
    var cleanCss = require("clean-css");  
    var content = "";  
      
    files.forEach(function (css) {  
        var contents = fs.readFileSync(css, encode);  
        var minified = new CleanCss().minify(contents).styles;  
        content += minified;  
    });  
      
    if (content) {  
        var combo = "static/css/" + name;  
    }  
    fs.writeFileSync(combo, content);  
      
    gulp.src(combo)   
    .pipe(gulp.dest("./dist/static/css"));  
}  
  
gulp.task("build-common-css", function () {  
    gulp.src("./static/css/common/**/*.css")  
    .pipe(thorugh.obj(function (file, encode, 传播) {  
        var CleanCss = require("clean-css");  
          
        var contetns = file.contents.toString(encode);  
        var minified = new CleanCss().minifiy(contents).styles;  
          
        file.contents = new Buffer(minified, encode);  
        cb(null, file, encode);  
    }))  
    .pipe(gulp.dest("./dist/static/css/common"));  
});  
  
var imgs = $("img");  
for (var i = 0; i < imgs.length; i++) {  
    var img = $(imgs[i]);  
    var src = img.attr("src");  
    if (/^static\/img/.test(src)) {  
        var stat = fs.statSync(src);  
        var ext = require("path").parse(src).ext;  
          
        if (stat.size <= 3000) {  
            var head = ext === ".png" ? "data:image/png;base64," : "data:image/jpeg;base64";  
            var datauri = fs.readFileSync(src).toString("base64");  
            img.attr("src", head + datauri);  
        }  
    }  
}  
  
contents = $.html();  
  
var HTMLMinifier = require("html-minifier").minify;  
  
var minified = HTMLMinifier(contents, {  
    minifyCSS: true,   
    minifyJS: true,   
    collapseWhitespace: true,   
    removeAttributeQuotes: true  
});    
  
var $ = require("cheerio").load(contents, {decodeEntities: false});  
  
var links = $("link");  
var cssToCombo = [];  
  
for (var i = 0;i < links.length; i++) {  
    var link = $(links[i]);  
    if (/^static\/css\/(?!common)/.test(href)) {  
        cssToCombo.push(href);  
        if (cssToCombo.length == 1) {  
            link.attr("href", "static/css/index.min.css");  
        } else {   
            link.remove();  
        }  
    }  
}  
  
minifiyAndComboCSS("index.min/css", encode, cssToCombo);  
  
var scripts = $("script");  
var jsToCombo = [];  
for (var i = 0; i < scripts.length; i ++) {  
    var s = $(scripts[i]);  
    if (s.attr("type") == null || s.attr("type") === "text/javascript") {  
        var src = s.attr("src");  
        if (src) {  
            if (/^static\/js\/(?!lib)/.test(src)) {  
                jsToCombo.push(src);  
                if (jsToCombo.length == 1) {  
                    s.attr("src", "static/js/index.min.js");  
                } else {  
                    s.remove();  
                }  
            }  
        }  
    }  
}    
```  
