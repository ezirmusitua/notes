## Windwos 下的命令行配置  
![这篇文章](https://getpocket.com/a/read/1359586366)  
介绍了一些改善 Windows 下命令行体验的方法  
和其他平台的命令行相比，windows 下的体验命令行究竟差在哪里?  
1. 难看  
2. 缺乏辅助功能  
3. 功能缺失  

一种改进方法:  
使用 windows 下的模拟 Linux 环境工具，如 Mingw 和 Cygwin，其实是很蛋疼的一件事情，速度尴尬，不兼容的情况  
类似的改进方法:  
如 babun 和 gow 等，将部分 Linux 下的工具整合出来到 Windows 下使用，用过一段时间，感觉并没有什么用，本质上和 上面的方法是没什么区别的  

使用 Powershell 和 ConEmu 改进 Windows 下的命令行体验  

### Powershell  
离开 Cmd 进入 Powershell 的怀抱  
1. 什么是 Powershell  
![维基百科](https://zh.wikipedia.org/wiki/Windows_PowerShell)中的定义  
2. 为什么使用 Powershell  
基于 .NET Framework + CommandLine Shell + Script Language  
自动化工具

### Powershell 插件
1. https://github.com/tkellogg/Jump-Location
一种类似 autojump 的工具  

2. https://github.com/dahlbyk/posh-git
自定义 prompt  

3. PSReadline -- win10 自带  
优化 Powershell 

4. https://www.zhihu.com/question/29550357
一个现成的配置方案  


### ConEmu  
环境配置  
各种样式上的东西，看个人
最主要的是 key 的配置  
个人设置的几个常用配置如下:  
> 快速唤醒 -- Alt+`  
> 新建窗口 -- LAlt + E  
> 水平分割窗口 -- LCtrl + LAlt + H  
> 垂直分割窗口 -- LCtrl + LAlt + K 
> 向上移动窗口边界 -- Win +   LCtrl + H
> 向下移动窗口边界 -- Win +   LCtrl + L    
> 向左移动窗口边界 -- Win +   LCtrl + J  
> 向右移动窗口边界 -- Win +   LCtrl + K  
> 选择左边的窗口 -- LCtrl + LShift + J 
> 选择右边的窗口 -- LCtrl + LShift + K 
> 选择左边的 Tab -- LAlt + J  
> 选择右边的 Tab -- LAlt + K 
> 关闭当前 Tab -- LAlt + Q 
> 复制当前 Tab -- LCtrl + Win + D 
> 在当前 Tab 中查找 -- LCtrl + Win + F 
> 在当前 Tab 中截屏 -- LCtrl + Win + H  
善用`屏幕分割`，在 windows 下拥有 tmux 的分屏功能  
