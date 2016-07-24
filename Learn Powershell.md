## Powershell 学习  
### 管道  
ls | sort -Descending Name | Format -Table Name, Mode  
### 重定向  
追加 >> 
覆盖 >

### 数学计算  
+ - * / % ()  
容量大小 \*b  

### 启动命令  
netstat -- 网络端口  
ipconfig -- 网络配置 
route print -- 路由信息  
cmd /c help  
&"ls" -- 执行字符串  

### 命令集  
[cheat sheets](http://www.pstips.net/powershell-cmdlets.html)  

### 别名  
ls alias: -- 查看所有别名  
ls alias: | Group-Object Definition | sort -Descending Count
Set-Alias -Name vim -Value gvim
利用函数带参数设置别名:  
```
function test-conn { Test-Connection  -Count 2 -ComputerName $args}
Set-Alias tc test-conn
tc localhost
```  

### 执行脚本  
.bat  
.vbs -- cscript.exe .test.vbs
.ps1:  
```  
File ” C:\PS\test.ps1″ cannot be loaded because the
execution of scripts is disabled on this system. Please see
“get-help about_signing” for more details.
At line:1 char:10
+ .test.ps1 <<<<
这是powershell的默认安全设置禁用了执行脚本，要启用这个功能需要拥有管理员的权限。
```  

### 定义变量  
$variable = value;
遍历当前变量:  
ls valiable:  
查找变量:  
ls valiable:var\*  
删除:  
del valiable:var\*  
专有变量操作:  
clear/get/new/remove/set  
变量写保护:  
New-Variable num -Value 100 -Force -Option readonly
变量描述:  
new-variable name -Value "me" -Description "This is my name"

### 自动化变量  
打开 Powershell 自定加载的变量:  

1. 用户信息  
2. 配置信息  
3. 运行时信息  

[列表](http://www.pstips.net/powershell-automatic-variables.html)  

### 环境变量  
1. 读取:  
$env:windir
2. 查找:  
会查找 User ENV 和 SYS ENV  
ls env:
$env:name
3. 创建  
$env:TestVar1="This is my environment variable
4. 删除和更新  
del env:windir
$env:OS="Redhat Linux"
**重新打开恢复如初**  
```  
[environment]::SetEnvironmentvariable("Path", ";c:\powershellscript", "User")
```  
保持

### 驱动器变量  
1. 访问  
防止误操作，放到 {} 中  
${c:/powershell/ping.bat}  
2. 特殊变量: 子表达式  
"The size of Powershell_Cmdlets.html is $($(ls Powershell_Cmdlets.html).Length)"

### 作用域  
四个作用域:  

* 全局  
* 当前  
* 私有  
* 脚本  

更改变量可见性:  
> 运行脚本时使用一个原点和空格，Powershell解释器就不会为脚本本身创建自己的变量作用域，它会共享当前控制台的作用域，这种不太灵活但却简单的方法，使用时一定要格外小心  

设置单个变量作用域  
$global
$script
$private
$local

使用修饰符增加变量作用域 ~  

### 变量的类型和强类型  
1. 获取类型  
var.gettype().name  

2. 指定变量类型  
[byte]$b = 101  

### powershell 变量管理幕后  
1. 创建变量  
2. 后台生成 PSVariable  
[更多](http://www.pstips.net/powershell-variable-management-behind-the-scenes.html)  

### 



