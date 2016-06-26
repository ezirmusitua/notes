[来源](http://aerotiger.info/archives/beginners-guide-node-package-manager.html?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)  

全局安装: 在电脑中全局安装  
相对安装: 在项目中单独安装  

更改全局安装目录:  
```  
$ npm config  
$ npm config list  
# 列出安装目录  
$ npm config get prefix  
# 设置全局安装目录  
$ npm config set prefix=$HOME/.node_modules_global  
# 重新安装 npm 到新配置目录  
$ npm install npm --global  
# 设置环境变量  
$ export PATH="$HOME/.node_modules_global/bin:$PATH"  
```  

在全局安装:  
```  
# 列出已安装  
$ npm list --global  
$ npm list -g  
# 只显示一级  
$ npm list --global --depth=0  
```  

在项目中安装  
```  
# 初始化 package.json (if necessary)  
$ npm init  
# 添加 --yes 参数使用默认配置  
$ npm init --yes  
# 为 package.json 添加相关依赖  
# 使用 npm install 安装  
$ npm install  
```  

安装特定版本模块  
```  
# 安装大于等于 2.6.2 版本的 uglyfy-js 
$ npm install uglyfy-js@~2.6.2  
```  

卸载模块  
```  
$ npm uninstall uglyfy-js  
```  

更新模块  
```  
$ npm update hexo -g  
```  

搜索模块  
```  
# 使用 search 查找  
$ npm search mkdir  
# 使用 view 查看细节  
$ npm view mkdirp  
```  

管理缓存  
在 ~/.npm 中, 定期清理  
```  
$ npm cache clean  
```  

手动安装 package 时保持 package.json 同步  
```  
npm install uglyfy-js --save  
```  

管理多版本 Node.js: `nvm`  

