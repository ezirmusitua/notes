## MDN CSS 入门教程  
### 层叠和继承  
来源：  
 - 浏览器  
 - 用户自定义  
 - 开发者定义  
   - 外链  
   - 内联  
   - 行内  

**!important**关键字允许用户覆盖开发者样式  

### 选择器  
**选择器间存在优先级**  
**选择器间可以自由组合**  
#### 伪类选择器  
选择器后指定元素状态的关键字  
```  
a:hover {
  color: bule;
}  
```  

### 基于关系的选择器  
`A E` 元素 A 的后代元素 E  
`A > E` 元素 A 的直系后代  
`E: first-child` 任意是其`父`节点的`第一个``子`节点的元素 E  
`B + E` 元素 B 的任一下一个兄弟元素 E  
`B ~ E` B 元素后拥有共同父元素的兄弟元素 E  
`*` 任意元素  
**任意组合**  

### 可读性良好的 CSS  
1. 空格  
2. 注释 /**/  
3. 选择器组  

### 文本样式  
[Font](http://www.w3.org/TR/CSS21/fonts.html):  
1. font-family -- 字体  
2. font-size -- 大小  
3. line-height -- 文本间距  
4. text-decoration -- 下划线等  
5. font-style -- 斜体等  
6. font-variant: small-caps -- 小型大写字母  

### 颜色  
颜色形式:  
1. 17 中名称  
2. 三位 hex  
3. 六位 hex  
4. rgb()

颜色属性:  
1. background-color: 背景颜色  
2. color: 字体颜色  

### 内容  
#### 文本内容  
```  
p {
  content: "Hello: ";
}  
```  
在每个 p 标签的内容前面增加 Hello  
#### 图片内容  
1. 在 `content` 中增加 url()  
2. 设置 background 属性为: url()  

### 列表 
[list-style](https://developer.mozilla.org/zh-CN/docs/Web/CSS/list-style) 的更多细节
#### 无序列表  
`ul`, `li`  
样式标记:  
list-style: circle/ disc  
#### 有序列表  
`ol`, 'li'  
样式标记:  
list-style: decimal, lower-roman, upper-roman, lower-latin, upper-latin
#### 计数器  
**有兼容性问题**  
可以计算任何元素的数目  
使用方法：  
1. 定义计数器  
2. 计数开始前某个元素上设置 `reset-counter` 属性重置计数器  
3. 设置每个需要计数元素的 `counter-increment` 为计数器名称  
4. 通过为选择器增加 `:before` 和 `:after` 并设置 content 属性显示计数器  
5. 在 `content` 中设置 `conter(counter-name[, counter-type])` 

### 盒模型  
`margin`, `padding`, `border` 分别对应着外边距，内衬距以及边界  

### 布局  
[参考](http://zh.learnlayout.com/position.html)  
1. 使用 `div` 标签建立文档结构  
2. 大小单位 -- px, em, rem  
3. 文本布局 -- text-align, text-indent  
4. 浮动 -- float -- left, right  
5. 位置 -- position -- relative, fixed, absolute, static  

### 表格  
使用 `caption` 标签为表格增加 title  

### 信息媒体  
使用 `@media` 对特定媒体指定适配规则  
1. screen  
2. print  
3. projection  
4. all  






