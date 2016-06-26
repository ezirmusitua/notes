## What is WebPack  
**模块加载器兼打包工具**  
把各种资源, 例如JS(含JSX)、coffee、样式(含less/sass)、图片等都作为模块来使用和处理  
![](http://images0.cnblogs.com/blog2015/561179/201507/161453372048661.jpg)  
[官网](http://webpack.github.io/), [文档](http://webpack.github.io/docs/)  

## 优势  
1. webpack 以 commonJS 的形式来书写, 对 AMD/CMD 的支持也很全面, 方便旧项目进行代码迁移. 
2. 能被模块化的不仅仅是 JS.  
3. 开发便捷, 能替代部分 grunt/gulp 的工作, 比如打包/压缩混淆/图片转base64等.  
4. 扩展性强, 插件机制完善, 特别是支持 React 热插拔(见 react-hot-loader)的功能.  

## 配置  
`webpack.config.js`  
```  
var webpack = require('webpack');  
var commonsPlugin = new webpack.optimize.CommonsChunkPlugin('common.js');  
  
module.exports = {  
    plugins: [commonsPlugin],  
    entry: {  
        index : './src/js/page/index.js'  
    },  
    
    output: {  
        path: 'dist/js/page',  
        filename: '[name].js'  
    },  
    module: {  
        loaders: [  
            { test: /\.css$/, loader: 'style-loader!css-loader' },  
            { test: /\.js$/, loader: 'jsx-loader?harmony' },  
            { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},  
            { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'},  
        ]  
    },  
    resolve: {  
        root: 'E:/github/flux-example/src',  
        extensions: ['', '.js', '.json', '.scss'],  
        alias: {
            AppStore: 'js/stores/AppStores.js',  
            ActionType: 'js/actions/ActionType.js',  
            AppAction: 'js/actions/AppAction.js'
        }  
    }  
};  
```
## 运行  
`webpack --distplay-error-details`  
其他参数:  
* webpack --config XXX.js  # 使用其他配置文件  
* webpack --watch  # 监听并自动打包  
* webpack -p  # 压缩混淆脚本  
* webpack -d  # 生成 map 映射文件, 告知哪些模块最终被打包到哪里去了  

## 模块引入  
### HTML  
**直接在页面引入 webpack 最终生成的页面脚本即可**  
### JavaScript  
**各脚本模块可以直接使用 commonJS 来书写, 并可以直接引入未经编译的模块, 比如 JSX、sass、coffee等(只要你在 webpack.config.js 里配置好了对应的加载器)**  
```  
require('../../css/reset.scss');  
require('../../css/allComponent.scss');  
var React = require('react');  
var AppWrap = require('../component/AppWrap');  
var createRedux = require('redux').createRedux;  
var Provider = require('redux/reat').Provider;  
var stores = require('AppStore');  
  
var redux = createRedux(stores);  
  
var App = react.createClass({  
    render: function () {  
        <Provider redux={redux}>  
            {function () {  
                return <AppWrap />;  
            }}  
        </Provider>  
    }  
});  
  
React.render(  
    <App />, document.body  
);  
```

## 其他  
### shimming  
**在 AMD/CMD 中, 我们需要对不符合规范的模块(比如一些直接返回全局变量的插件)进行 shim 处理, 这时候我们需要使用 exports-loader: 
```  
{ test: require.resolve("./src/js/tool/swipe.js"),  loader: "exports?swipe"}  
```
脚本中引用  
```  
require('./tool/swipe.js');  
swipe();   
```

### 自定义公共模块提取  
更加个性化的配置:  
```  
var CommonsChunkPlugin = require("webpack/lib/optimize/CommonsChunkPlugin");  
module.exports = {  
    entry: {  
        p1: "./page1",  
        p2: "./page2",  
        p3: "./page3",  
        ap1: "./admin/page1",  
        ap2: "./admin/page2"  
    },  
    output: {  
        filename: "[name].js"  
    },  
    plugins: [  
        new CommonsChunkPlugin("admin-commons.js", ["ap1", "ap2"]),  
        new CommonsChunkPlugin("commons.js", ["p1", "p2", "admin-commons.js"])  
    ]  
};  
// <script>s required:  
// page1.html: commons.js, p1.js  
// page2.html: commons.js, p2.js  
// page3.html: p3.js  
// admin-page1.html: commons.js, admin-commons.js, ap1.js  
// admin-page2.html: commons.js, admin-commons.js, ap2.js  
```

### 独立打包样式文件  
希望项目的样式能不要被打包到脚本中, 而是独立出来作为.css, 然后在页面中以<link>标签引入.需要 extract-text-webpack-plugin:  
```  
var webpack = require('webpack');  
    var commonsPlugin = new webpack.optimize.CommonsChunkPlugin('common.js');  
    var ExtractTextPlugin = require("extract-text-webpack-plugin");  
  
    module.exports = {  
        plugins: [commonsPlugin, new ExtractTextPlugin("[name].css")],  
        entry: {  
        //...省略其它配置  
```

### 使用 CDN 或远程文件  
希望某些模块使用 CDN 并以`<script>`的形式挂载到页面上来加载,同时希望能在 webpack 的模块中使用上, 使用 externals 属性:  
```  
{  
    externals: {  
        // require("jquery") 是引用自外部模块的  
        // 对应全局变量 jQuery  
        "jquery": "jQuery"  
    }  
}  
```

### 与 gulp 配合  
```javascript
gulp.task("webpack", function(callback) {  
// run webpack  
	webpack({// configuration}, function(err, stats) {  
    	if(err) {
        	throw new gutil.PluginError("webpack", err);  
    	}
    	gutil.log("[webpack]", stats.toString({  
        	// output options  
    	}));  
    	callback();  
	});
});
```
### React 相关  
* 推荐使用 npm install react 的形式来安装并引用 React 模块, 而不是直接使用编译后的 react.js, 这样最终编译出来的 React 部分的脚本会减少 10-20 kb左右的大小.  
* react-hot-loader 是一款非常好用的 React 热插拔的加载插件, 通过它可以实现修改-运行同步的效果, 配合 webpack-dev-server 使用更佳!  

## 相关资源  
[webpack入门指谜](https://segmentfault.com/a/1190000002551952)  
[webpack-howto](https://github.com/petehunt/webpack-howto)  