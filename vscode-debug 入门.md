## 构建项目  
`task.json`用于配置项目构建过程  
`Ctrl + Shift + P/B`  
```  
{  
    "version": "0.1.0",  
  
    // 要使用的命令或者可执行文件的路径  
    "command": "tsc",  
  
    // 对应command参数，是否是一个命令，否则为执行文件路径  
    "isShellCommand": true,  

    // 是否在执行task任务时显示控制台窗口  
    "showOutput": "always",  

    // 对应command参数指定程序的参数  
    "args": ["-p", "src", "--allowJs", "-w"],  
  
    // 不太明白这个，基本用不到  
    "problemMatcher": "$tsc",  
}  
```  
[更多参数和配置](https://code.visualstudio.com/docs/editor/tasks)  


## 调试视图  
一只蜘蛛啊~  

## 启动配置  
调试试图顶部栏中的齿轮图标  
自动生成 launch.json  

VSCode 支持以调试模式启动应用, 或调试一个已运行的应用:  
这取决于当前配置中 request 的取值(attach 还是 launch), 这个属性是必需的, 如果你忘记了如何赋值也没关系，填写时 VSCode 会提示你的  
视图顶部栏的下拉菜单可以指定需要以哪个配置运行, 如果选择的是Launch，那么接着按下F5就可以启动调试了  
如需在启动调试前执行某个特定任务(task), 为 preLaunchTask 设置 tasks.json 中的任务名即可  
  
## 断点  
单击编辑器侧边缘即可为所在行添加断点, 调试时BREAKPOINTS区域中也可对断点进行操作  
  
## 数据审查  
调试视图的 VARIABLES 区域可以审查变量, 也可以直接在源码上悬停鼠标来查看  
![](https://segmentfault.com/img/bVrwaR)  
变量和表达式的演算则显示在 CALL-STACK 区域中  
在调试视图的WATCH区域也可以看到变量和表达式的演算.  
![](https://segmentfault.com/img/bVrwaS)  

## 调试控制台  
在调试控制台可以计算表达式  
打开调试控制台需要 Open Console, 该命令可以在调试视图顶部栏和命令面板中被找到. (默认已经开启)  

## 调试命令  
调试一旦启动，编辑器顶部就会显示出调试命令栏  
![](https://segmentfault.com/img/bVrwaW)  
* 继续 / 暂停 F5  
* 跳过 F10  
* 进入 F11  
* 退出 Shift + F11  
* 重新开始 unassigned  
* 停止调试 Shift + F5  

## 调试Node  
### Node控制台  
调试Node时，默认启用的是 VS Code 内置的控制台  
内置的调试控制台不支持输入,  如果你需要从控制台输入,  那么你应该使用外部控制台,  在启动配置中设置 externalConsole 为 true 即可(现在内置的也支持输入了)  

### 断点验证  
出于性能的考虑, Node.js 解析 js 文件中的函数时采用了"懒加载"模式  
其结果是, 未被 Node.js 解析的源码中的断点不会起作用  
这一点对于调试工作来说自然是不理想的,所以在调试时 VS Code 自动添加了 --nolazy 选项  
这样就可以阻止延迟解析, 并确保断点可以在代码运行前被正常地触发了  
--nolazy 选项会导致启动时间大大增加, 如果你不希望如此, 添加 --lazy 来作为 runtimeArgs 的属性即可  
实际调试工作中: 
你可能会发现一些你设置的断点没有在预期位置被触发, 而是跳到了下一个可执行行  
为了避免混淆, VS Code 将实时显示断点的位置(Node.js 判断)  
在BREAKPOINTS区域, 断点将显示出其被触发的位置以及其实际所在行:
![](https://segmentfault.com/img/bVrwaX)  
启动一个已添加好断点的调试, 或者在一个已运行的调试中添加了一个新的断点, 断点都有可能会跳至其他位置  
在 Node.js 解析完所有代码之后, 使用 BREAKPOINTS 区域顶部的 Reapply 按钮可重置断点, 可以让断点重新跳回到触发位置  
![](https://segmentfault.com/img/bVrwa2)  

### JavaScript源码关联
VSCode 的 Node.js 调试器支持 JavaScript 源码映射关联, 这将有助于调试一些转译语言:  
1.TypeScript  
2. 被压缩过的JavaScript  
设置启动配置中的sourceMaps属性为true可以开启源码关联, 关联的源码将允许单步调试以及在其中添加断点  
另外, 你可以使用 program 属性来指定源码文件(如: app.ts)  
如果被生成的 js 文件并不是和源码在同一目录, 而是分散放在了其他的路径中, 你可以使用 outDir 属性来帮助调试器找到这些文件  
无论何时你在转译语言源码中设置了断点, VSCode 都会尝试在 outDir 目录下生成的 JavaScript 源码中找到相应位置去映射关联  
源码映射关联并不是自动创建的, 你必须通过配置TypeScript编译器来完成:  
`tsc --sourceMap --outDir bin app.ts`  
TypeScript调试配置示例:  
```  
{  
    "version": "0.2.0",  
    "configurations": [  
        {  
            "name": "Launch TypeScript",  
            "type": "node",  
            "request": "attach",  
            "program": "app.ts",  
            "sourceMaps": true,  
            "outDir": "bin"  
        }  
    ]  
}  
```  

### 调试外部Node
如果你想要使用 VSCode 的调试器来调试外部 Node.js 程序, 命令行示例:  
```  
$ node --debug program.js  
$ node --debug-brk program.js  
```  
--debug-brk选项用于让程序停止在程序源码的第一行  
相应的启动配置示例：
```  
{  
    "version": "0.2.0",  
    "configurations": [  
        {  
            "name": "Attach to Node",  
            "type": "node",  
            "request": "attach",  
            "port": 5858  
        }  
    ]  
}  
```  
