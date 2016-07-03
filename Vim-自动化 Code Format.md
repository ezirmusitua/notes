## 自动化 Code Format  
尝试使用 `vim-autoformat`，但因为使用的 `vim` 版本不是支持 Python 的编译版本，因此决定使用 Python 编写一个脚本，使用 vim 调用进行操作  
### Formatter 列表  
[参考](https://github.com/Chiel92/vim-autoformat)  
1. Python -- yapf  
2. jscs -- javascript  
4. Artistic Style -- C, C++, C++/CLI, Objective-C, C#, Java

### 相关设置  
```  
yapf --style google -i FILE_NAME  
jscs --preset airbnb FILE_NAME--fix 

