## 学习 CSS 布局  
[来源](http://zh.learnlayout.com)

### display  
block and inline 
块级元素和行内元素  
1. block  
会新开始一行并且尽可能撑满容器  
2. inline  
在段落中包裹文字而不会打乱布局  
3. none  
不显示  
与 visibility 不同，不会保留显示区域  

### margin auto  
1. 设置块级元素的 width 可以阻止左到右撑满容器  
2. 设置左右外边距为 auto 使之居中  


### max-width  
更好的处理小窗口情形  
与 width 不同，窗口变小会随之改变  

### 盒模型  
当你设置了元素宽度，世纪展现的额元素却能够超出你的设置:  
> 因为元素的边框和内边距会撑开元素  
```  
{
  box-sizing: border-box;
}  
```  
此元素内边距和边框不会增加他的宽度  
**正确的使用 -webkit/-moz** 来保证不同浏览器的兼容性  

### position   
1. static  
不会被特殊定位，不会被 `positioned`  
2. relative  
单独添加 relative，与 static 一致  
添加 top，right，bottom，left 属性会使其偏离原来的位置，其他元素不会调整位置来弥补空隙  
3. fixed  
相对视窗定位  
即使页面滚动也保留在相同位置  
有 top，right，bottom，left 属性  
移动浏览器支持很差，[解决](http://bradfrostweb.com/blog/mobile/fixed-position/)  
4. absolute  
类似 fixed   
相对于最近的 positioned 祖先元素  
没有 positioned 祖先，则相对 body 元素  
随着页面滚动而滚动  

### float  
可用于实现文字环绕效果  
```  
img {
  float: right;
  margin: 0 0 1em 1em;
}  
<p>
  <img src="/images/ex.jpg">
  Lorem ipsum dolor sit amet, consectetur adipiscing elit. Phasellus imperdiet, nulla et dictum interdum, nisi lorem egestas odio, vitae scelerisque enim ligula venenatis dolor. Maecenas nisl est, ultrices nec congue eget, auctor vitae massa. Fusce luctus vestibulum augue ut aliquet. Mauris ante ligula, facilisis sed ornare eu, lobortis in odio. Praesent convallis urna a lacus interdum ut hendrerit risus congue. Nunc sagittis dictum nisi, sed ullamcorper ipsum dignissim ac. In at libero sed nunc venenatis imperdiet sed ornare turpis. Donec vitae dui eget tellus gravida venenatis. Integer fringilla congue eros non fermentum. Sed dapibus pulvinar nibh tempor porta. Cras ac leo purus. Mauris quis diam velit.
</p>
```  

### clear  
控制浮动  
```  
clear: left/right   
```  
清除浮动  
一个 element 的 size 大于另一个 element 的时候会产生无法包含的情况  
使用 overflow 属性来清除  

### 百分比宽度  
相对于包含块的计量单位  
对图片很有用  

### 响应式设计  
让网站针对不同浏览器和设备相应不同显示效果的策略  
```  
@media screen and (min-width: 600px) {
    // ... 
}
@media screen and (max-width: 599px) {
    // ... 
}
```  
使用 [meta viewport](http://dev.opera.com/articles/view/an-introduction-to-meta-viewport-and-viewport/) 可以让布局在移动浏览器上显示更好

### inline-block  
old -- 使用 `float` 布局  
```  
.box {
    float: left;
    width: 200px;
    heigth: 100px;
    margin: 1em;
}

.aft-box {
    clear: left;
}  
```  
now -- 使用 `inline-block` 布局  
```  
.box2 {
    display: inline-block;
    width: 200px;
    height: 100px;
    margin: 1em;
}
```  

**MUST REMEMBER**  
1. `vertical-align` 属性会影响到 `inline-block`，你可能会把它设置为 `top`  
2. 需要设置每一列的宽度  
3. HTML 源代码中的空格会是每一列间产生空隙  

### 多列布局  
CSS columns  
一个非常新的标准，需要使用前缀  
不被 IE9 一下以及 OperaMini 支持  
这里的`列`指的并不是 `bootstrap` 中的 column，而是内容多了以后会以多列的形式展示  

### flexbox  
新的标准  
仔细甄别材料，选择新的 flexbox 教程  




