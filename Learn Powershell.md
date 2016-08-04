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

## Powershell 数组和哈希表  
### 命令返回数组
```  
$ip = ipconfig  
$ip -is [array]  
```  
使用 Format-List * 查看对象的属性和方法  

### 创建数组  
1. $num = 1, 2, 3, 4, 5  
2. $num = 1..5(Number only)
3. $multiTypeArray = 1, "2012 world cup", ([System.Guid]::NewGuid()), (get-date)  
4. $emptyArray = @()  
5. $singleElementArray = ,5  

### 访问数组  
1. from 0 -> arrayLength - 1 / -1  
2. multi element: $array[0, 4, 8]  
3. reverse array: $array[($array.Count)..0]  
4. insert: $array += 5  
5. delete: $array = $array[0..1] + $array[3] // delete 2  

### 复制数组  
*默认复制引用*  
Clone:  
```  
$chs = @("A", "B", "C")  
$chBak = $chs.Clone()  
```  

### 强类型数组  
统一数组中元素类型  
```  
[int[]] $number = @()  
```  

### 使用 hash table  
key-value  
1. create
```  
$stuHash = @{name="ming"; age=12; sex="male"; books="wu haha ~ "}  
```  

2. insert
```  
$stuHash = @{}  
$stuHash.name = "ming"  
```  

3. update  
```  
$stuHash = @{name="ning"}  
$stuHash.name = "ming"  
```  

4. delete  
$stuHash = @{name="ming"}  
$stuHash.remove(name)  

5. Format table  
```  
Dir | Format-Table  
```  
Expression -- 绑定的表达式  
Width -- 列宽度  
Label -- 列标题  
Alignment -- 列对齐方式  

## Powershell 管道  
### 使用  
1. 面向对象的管道  
每个命令的末尾可以使用新的命令对上一个命令进行处理(除非以管道输出命令结尾)  
2. 常用命令:  
    * Compare-Object  
    * ConvertTo-Html  
    * ExportTo-Clixml  
    * ExportTo-Csv  
    * ForEach-Object  
    * Format-Table  
    * Format-Wide  
    * Get-Unique  
    * Group-Object  
    * Import-Clixml  
    * Measure-Object  
    * more  
    * Out-File  
    * Out-Null  
    * Out-Printer  
    * Out-String  
    * Select-Object  
    * Sort-Object  
    * Tee-Object  
    * Where-Object  

3. 处理模式
    * 顺序  
    * 流  

4. 阻塞问题  

### 对象 To 文本  
*Out-default*  

*Get-Command -Verb Format*  

\# 显示指定属性  
```  
ls | Format-Table Name, Length, LastWriteTime  
```  
*通配符*  
```  
Get-Process i* | Format-Table Name, pe*64  
```  

*修改列标题*  
```  
$column = !{Expression={ [int](_.Length/1kb) }; Label="KB" }
Dir | Format-Table Name, $column  
```  

*优化列宽 -auto*  

### 排序和分组  
Sort-Object -Descending  
```  
Dir | Sort-Object @{expression="Length";Descending=$true},@{expression="Name";Ascending=$true}  
```  
Group-Object:  
```  
Get-Service | Group-Object Status  
ls | Get-Object {$_.name.SubString(0, 1).toUpper()}  
Dir | Sort-Object Extension, Name | Format-Table -groupBy Extension
```  

### 过滤  
```  
Get-service | Select-Object -First 1 | Get-Member -MemberType
Get-service | Where-Object {$_.Status -eq "Running"}
# 属性
Get-WmiObject Win32_UserAccount -filter "LocalAccount=True AND Name='guest'"
# 数量
Dir | Select-Object -ExcludeProperty "*N*" -First 5
# 逐个处理  
ls | ForEach-Object {"文件名： 文件大小(M): " -f $_.Name,$_.Length/1M}
# 去重 
1,2,1,2 | Get-Unique
```  

### 分析和比较管道结果  
```  
# 计算  
ls | measure length  
# 统计  
Get-Content .word.txt | measure -Line -Word -Character
# 比较对象  
$before=Get-Process
$after = get-process 
ompare-Object $before $after

# 检查对象变化  
$svc1=Get-Service wsearch
$svc1.stop()
$svc2=Get-Service wsearch
Compare-Object $svc1 $svc2 -Property Status,Name

# 快照  
Get-Process  | Export-Clixml before.xml
$before=Import-Clixml .before.xml
$after=Get-Process
Compare-Object -ReferenceObject $before -DifferenceObject $after
```  

### 导出管道结果  
`get-command -Verb out`
```  
# 吸收  
md ABD >$null
md ABE | Out-Null
# 修改输出格式  
pwd;Get-Service
# 强制文本输出  
ls . -Recurse | Out-String
# CSV  
Get-Service | Export-Csv a.csv
# HTML  
Get-Service | ConvertTo-Html -Title "ls result" | Out-File a.html
```  

### 扩展类型系统  
*ETS*  
[参考](http://www.pstips.net/powershell-extended-type-system-1.html)  


## 对象  
### 对象 = 属性 + 方法  
1. 创建对象  
```  
$pocketKnife = New-Object object  
$pocketKnife  
```  
2. 增加属性  
```  
Add-Member -InputObject $pocketKnife -Name Weight -Value "55" -MemberType NoteProperty  
$pocketknife | Add-Member NoteProperty Blades 3  
$pocketknife | Add-Member NoteProperty Manufacturer ABC
```  

3. 增加方法  
```  
# 增加一个新方法:
Add-Member -memberType ScriptMethod -In $pocketknife `
-name cut -Value { "I'm whittling now" }
# 指定参数类型增加一个新方法:
Add-Member -in $pocketknife ScriptMethod screw { "Phew...it's in!" }
# 直接通过管道增加一个新方法:
$pocketknife | Add-Member ScriptMethod corkscrew { "Pop! Cheers!" }
```  

### 属性: 描述对象是什么  

### 方法: 对象做什么  

### 使用  

### 静态方法  
查看类型支持的所有静态方法  
```  
[System.DateTime] | Get-Member -static -memberType *Method  
```  

可以使用感兴趣的 .NET 类型  

查看程序集  

### COM 对象  

### 条件操作符  
* -eq  ===  
* -ne  !==  
* -gt  >  
* -ge  >=  
* -lt  <  
* -le  <=  
* -contains  
* -notcontains   

取反 -not  

布尔运算  
-and  
-or  
-xor  
-not  

where -- ?  

### IF-ELSEIF-ELSE  
```  
if($n -lt 0 ){"-1" } elseif($n -eq 0){"0"} else {"1"}  
```  

### SWITCH  
```  
switch($value)
{
    {$_ -lt 5 }   { "小于5"; break}
    {$_ -gt 0 }   { "大于0"; break}
    {$_ -lt 100}  { "小于100"; break}
    Default {"没有匹配条件"}
}
```  
-case 大小写  
-wildcard 通配符  

### ForEach-Object 循环  
```  
Get-WmiObject Win32_Service | ForEach-Object {"Name:"+ $_.DisplayName, ", Is ProcessId more than 100:" + ($_.ProcessId -gt 100)}
```  

### Foreach 循环  
```  
foreach ($n in $array)
{
    $n*$n
}
```  

### while 和 do-while 循环  
```  
# do-while  
do { $n=Read-Host } while( $n -ne 0)  
# while  
while($n -gt 0) {
    $n
    $n=$n-1
}
# 终止当前  
$n=1
while($n -lt 6)
{
    if($n -eq 4)
    {
        $n=$n+1
        continue
 
    }
    else
    {
        $n
    }
    $n=$n+1
}
# 跳出循环  
$n=1
while($n -lt 6)
{
    if($n -eq 4)
    {
        break
    }
    $n
    $n++
}
```  

### For 循环  
```  
$sum=0
for($i=1;$i -le 100;$i++)
{
    $sum+=$i
}
$sum
```  

### Switch 循环  
```  
# 使用Switch循环    
$nums = 10..7
Switch ($nums) {
    Default { "n= $_" }
}
```  

## 函数  
### 定义函数  
```  
Function FuncName （args[]）{
      code;
}
```  
### 参数  
1. $args 万能参数  
2. 设置参数名称  
```  
function StringContact ($str1, $str2) {
}
```  
3. 默认值  
```  
function StringContact ($str1="123", $str2) {
}
```  
4. 强类型    
```  
function StringContact ([String]$str1="123", [String]$str2) {
}
```  

### 返回值  
可以返回多个或者使用 return 返回特定  

### Powershell 查看支持的函数  
```  
dir function: | ft -AutoSize
```  

### 函数过滤管道  
高级的东西  

- - -  
2016年8月3日23:04:49 -- 缺乏练习，暂时停止  










