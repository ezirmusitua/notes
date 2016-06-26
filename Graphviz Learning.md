## Graphviz 学习
### 基本   
1. 无向图  
```  
graph example_1 {
    a -- b;
    b -- c;
    b -- d;
    d -- a;
}
```  
增加布局:  
```  
graph example_2 {
    rankdir=LR;
    a -- b;
    b -- c;
    b -- d;
    d -- a;
}
```  
2. 简单有向图:  
```
digraph example_3 {  
    a -> b;  
    b -> c;  
    a -> c;  
}  
```  
带标签有向图:  
```  
digraph example_4 {
    T [label="Teacher"];  
    P [label="Pupil"];  
    T -> P [label="Instructions", fontcolor=darkgreen];  
}  
```  
设置颜色和大小:  
```  
digraph example_5 {
    T [label="Teacher" color=Blue, fontcolor=Red, fontsize=24, shape=box];  
    P [label="Pupil" color=Blue, fontcolor=Red, fontsize=24, shape=box];  
    T -> P [label="Instructions", fontcolor=darkgreen];  
}
```  
_形状_:  
> * box  
> * polygon  
> * ellipse  
> * oval  
> * cirle  
> * point  
> * egg  
> * triangle  
> * plaintext  
> * diamond  
> * trapezium  
> * parallelogram  
> * house  
> * pentagon  
> * hexagon  
> * septagon  
> * octagon  
> * doublecircle  
> * doublectagon  
> * tripleoctagon  

3. 总结  
```  
digraph summary {
    start [label="Start with a Node"]
    next [label="Choose your shape", shape=box]
    warning [label="Dont go overboard", colr=Blue, fontcolor=Red, fontsize=24, style=filled, fillcolor=green, shape=octagon]
    
    start -> next  
    start -> warning  
    next -> end [label="Getting Better ... ", fontcolor=darkblue]  
}  
```  

### 高级  
1. 快速定义节点和边特征  
```  
digraph hierarchy {  
    nodesep=1.0  
    node [color=Red, fontname=Courier, shape=box]
    edge [color=Blue, style=dashed]

    Headteacher -> { Deputy1 Deputy2 BusinessManager }  
    Deputy -> { Teacher1 Teacher2 }
    BusinessManager -> ITManager  
    { rank=same; ITManager Teacher1 Teacher2 }
}  
```  
2. `记录`结构  
使用 html 定义`记录`结构体  
```  
digraph structs {  
    node [shape=record]
    struct1 [label="<f0> left|<f1> mid\ dle|<f2> right"]
    struct2 [label="{<f0> one|<f1> two \n\n\n}" shape=Mrecord];
    struct3 [label="hello\nworld |{ b |{c|<here> d|e}| f}| g | h"]  
    struct1:f1 -> struct2:f0;
    struct1:f0 -> struct3:f1;
}  
```  
`<f0>`之类的是标签，由用户定义，在左边被生命，代表的是每一结构体中的各个部分  

### 一些例子  
1. 有限状态机  
```  
digraph finite_state_machine {
    rankdir=LR
    size="8,5"
    node [shape=circle]
    s0 -> s1 [label="Lift Nozzle"]
    s1 -> s0 [label="Replace Nozzle"]
    s1 -> s2 [label="Authorize Pump"]
    s2 -> s0 [label="Replace Nozzle"]
    s2 -> s3 [label="Pull Trigger"]
    s3 -> s2 [label="Release Trigger"]
}  
```  
2. 数据流示意图  
```  
digraph dfd {
    node [shape=record]  
    store1 [label="<f0> left|<f1> Some data Store"]  
    proc1 [label="{<f0> 1.0|<f1> Some process here\n\n\n}" shape=Mrecord]  
    enti1 [label="Customer" shape=box]  
    store:f1 -> proc1:f0  
    ent1 -> proc1:f0  
}  
```  
3. 数据流示意图 2   
```  
digraph dfd2 {  
    node [shape=record]  
    subgraph level0 {  
        enti1 [label="Customer" shape=box]  
        enti2 [label="Manager" shape=box]  
    }  
    subgraph cluster_level1 {
        label="Level 1"  
        proc1 [label="{<f0> 1.0|<f1> One process here\n\n\n}" shape=Mrecord]  
        proc2 [label="{<f0> 2.0|<f1> Other process here\n\n\n}" shape=Mrecord]  
        store1 [label="<f0>    |<f1> Data store one"]  
        store2 [label="<f0>    |<f1> Data store two"]  
        {rank=same; store1, store2}  
    }
    enti1 -> proc1  
    enti2 -> proc2
    store1 -> proc1  
    store2 -> proc2  
    proc1 -> store2  
    store2 -> proc1  
}  
```  

4. 对象继承  
```  
digraph obj {  
    node [shape=record]  
    rankdir="BT"  

    teacher [label="{<f0> Teacher | <f1> \n | <f2> \n }"]
    course [label="{<f0> Course | <f1> \n | <f2> \n }"]
    student [label="{<f0> Student | <f1> \n | <f2> \n }"]
    lesson [label="{<f0> Lesson | <f1> \n | <f2> \n }"]
    tutorial [label="{<f0> Tutorial | <f1> \n | <f2> \n }"]
    assessment [label="{<f0> Assessment | <f1> \n | <f2> \n }"]
    coursework [label="{<f0> Coursework | <f1> \n | <f2> \n }"]
    exam [label="{<f0> Exam | <f1> \n | <f2> \n }"]

    {rank=same; teacher course studen}  

    teacher -> course [dir="forward", arrowhead="none", arrowtail="normal", headlabel="1", taillabel="1..."]  
    student -> course [dir="forward", arrowhead="none", arrowtail="normal", headlabel="1", taillabel="1..."]  
    lesson -> course [dir="forward", arrowhead="diamond", arrowtail="normal"] 
    tutorial -> course [dir="forward", arrowhead="diamond", arrowtail="normal"] 
    assessment -> course [dir="forward", arrowhead="diamond", arrowtail="normal"] 
    coursework -> assessment  
    exam -> assessment  
}  
```  
5. 关系型实体  
```  
digraph ER {  
    node [shape=box]  
    Book  
    Customer  
    Loan  
    {rank=same; Book, Customer, Load}  
    Book -> Loan [dir="forward", arrowhead="crow", arrowtail="normal"]  
    Customer -> Loan [dir="forward", arrowhead="crow", arrowtail="normal"]  
}  
```  

### 常用属性  
1. 图像属性  
> label="My Label"     # 图像标签  
> rankdir=LR           # 上下布局变为左右布局  
> {rank=same; a, b, c} # 将元素放到同一个层级中  
> splines="line"       # 边框变为直线，没有曲线和锐角  
> K=0.6                # 影响布局中的 spring 属性  

2. 交点属性  
> [label="Some Label"] # 交点标签  
> [color="red"]        # 交点颜色  
> [fillcolor="blue"]   # 交点填充色  

3. 边属性  
> [label="Some Label"] # 边标签  
> [color="red"]        # 边颜色  
> [penwidth=2.0]       # 边厚度，路径标识

4. 尺寸，背景颜色  
> fixedsize=true       # ...  
> size="1.1"  
> resolution=72  
> bgcolor="#C6C6C6"  

- - -   

## 相关配置  
### 安装  
windows 下:
1. 官网下载  
2. 配置 PATH  

### 和 Vim 配合  
[wmgraphviz](https://github.com/wannesm/wmgraphviz.vim)  
遇到的问题:  
1. 使用我的 `update_plugins.py` 脚本无法正常安装  
原因未知  
解决方法是直接下载下来放到 `unfork` 文件夹中  

2. 使用 GraphCompile 指令报错  
原因是 windows 没有 tee 指令  
解决方法是下载 [wintee](https://code.google.com/archive/p/wintee/downloads) 保存到 graphviz(只要在 PATH 中就行) 并且重命名为 tee 即可  


### 一些配置  
1. vimrc 中新增  
```  
let g:WMGraphviz_out = "png"              " 修改输出格式为 png  
let g:WMGraphviz_viewer = "Imagine64.exe" " 修改默认图片浏览器，需要将该浏览器设置在 PATH 中  
```  

2. 修改 dot.vim  
修改 GraphvizShow 方法，使正常运行
```  
fu! GraphvizShow()
	if !filereadable(GraphvizOutputFile(g:WMGraphviz_output))
		call GraphvizCompile(g:WMGraphviz_dot, g:WMGraphviz_output)
	endif
	if !executable(g:WMGraphviz_viewer)
		echoerr 'Viewer program not found: "'.g:WMGraphviz_viewer.'"'
		return
	endif
	exec '!'.g:WMGraphviz_viewer.' '.shellescape(GraphvizOutputFile(g:WMGraphviz_output))
endfu
```  
修改 GraphvizInteractive 方法，使正常运行  
```  
if !exists('g:WMGraphviz_gvedit')
	let g:WMGraphviz_gvedit = 'gvedit'  
endif

fu! GraphvizInteractive()
	if !executable(g:WMGraphviz_gvedit)
		echoerr 'The "'.g:WMGraphviz_gvedit.'" executable was not found.'
		return
	endif

	exec '!'.g:WMGraphviz_gvedit.' -Txlib '.shellescape(expand('%:p'))
endfu
```  

修改 GraphvizOutputFile 方法，使输出的结果更具组织性
```  
fu! GraphvizOutputFile(output)
    if a:output == "log"
        let target = expand('%:p:h') . '\Log\'
    else     
    	let target = expand('%:p:h') . '\Out\'
	endif 
	if !isdirectory(target)
		 call mkdir(target, "p")
	endif
	return  target . expand('%:t:r') . '.' . a:output
endfu    
```  

**尝试增加 F5 自动显示的功能，失败了，还是直接用 :GraphvizShow 吧 ，，，**
