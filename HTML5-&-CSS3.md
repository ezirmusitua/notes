## HTML5 and CSS3  

### HTML5  
[参考](http://www.w3school.com.cn/html5/index.asp)  


#### 视频  
vedio tag  
格式: ogg mp4 webm  
```  
<vedio src="..." controls="controls"></vedio>
```  
方法:  
1. play()  
2. pause()  
3. load()  
4. cancelPlayType  


#### 音频  
audio tag  
格式: ogg mp3 wav  
```  
<audio src="..." controls="controls">
</audio>  
```  


#### 拖放  
相关属性:  
1. ondrop 事件  
2. draggable 属性  
3. ondragstart 和 setData  
4. ondragover  


#### Canvas  
画布  
创建:  
```  
<canvas id="myCanvas" width="200" height="300"></canvas>  
```  
**必须使用 Javascript 完成绘图**  
在使用时重新学习，一般用不到  


### 内联 SVG  
什么是 SVG:  
1. 可伸缩矢量图形  
2. 用于定义用于网络的基于矢量的图形  
3. 使用 XML 格式绘图  
4. 任意缩放  
5. 标准  

使用:  
```  
<!DOCTYPE html>
<html>
<body>
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" height="190">
  <polygon points="100,10 40,180 190,60 10,60 160,180"
  style="fill:lime;stroke:purple;stroke-width:5;fill-rule:evenodd;" />
</svg>
</body>
</html>
```  


#### 地理位置  
获取: `navigator.geolocation.getCurrentPosition(showPosition)`  
与地图 API 结合  
Geolocation 对象:  
watchPosition -- 返回用户当前位置  
clearWatch -- 停止 watch  


#### Web 存储  
localStorage 和 sessionStorage  


#### 应用程序缓存  
[什么鬼](http://www.w3school.com.cn/html5/html_5_app_cache.asp)  


#### Web Workers  
后台运行的 Javascript   
不影响页面性能  
```  
<!DOCTYPE html>
<html>
<body>
<p>Count numbers: <output id="result"></output></p>
<button onclick="startWorker()">Start Worker</button>
<button onclick="stopWorker()">Stop Worker</button>
<br /><br />
<script>
var w;
function startWorker() {
if(typeof(Worker)!=="undefined") {
  if(typeof(w)=="undefined") {
    w=new Worker("demo_workers.js");
  }
  w.onmessage = function (event) {
    document.getElementById("result").innerHTML=event.data;
  };
} else {
  document.getElementById("result").innerHTML="Sorry, your browser
    does not support Web Workers...";
  }
}
function stopWorker() {
    w.terminate();
}
</script>
</body>
</html>
```  


#### 服务器发送事件  
单向消息传递  
`EventSource()`  


#### 新的 Input 类型  
1. email  
2. url  
3. number  
4. range  
5. Date pickers (date, month, week, time, datetime, datetime-local)  
6. search  
7. color  


#### 表单元素  
1. datelist  
规定输入域的选项列表  

2. keygen  
提供一种验证用户可靠性的方法  

3. output  
不同类型的输出  


#### 表单属性  
1. 新的 `form` 属性  
* autocomplete  
* novalidate  

2. 新的 `input` 属性  
* autocomplete  
* autofocus  
* form  
* form override  
* height & width  
* list  
* min max step  
* multiple  
* pattern  
* placeholder  
* require  


### CSS3  

#### 边框  
1. border-radius  
添加边框弧度  

2. box-shadow  
添加边框阴影  

3. border-image  
使用图片创建边框  


#### 背景  
1. background-size  
背景图片大小  

2. background-origin  
规定背景图片的定位区域  
```  
border-box  
  padding-box  
    content-box  
```  

**背景允许设置多个图片**  
background-clip  
规定背景绘制区域  


#### 文本效果  
1. text-shaodw  
文本阴影效果  

2. word-wrap  
单词太长会超出某个区域  
自动换行 
> hanging-punctuation	规定标点字符是否位于线框之外。	3  
> punctuation-trim	规定是否对标点字符进行修剪。	3  
> text-align-last	设置如何对齐最后一行或紧挨着强制换行符之前的行。	3  
> text-emphasis	向元素的文本应用重点标记以及重点标记的前景色。	3  
> text-justify	规定当 text-align 设置为 "justify" 时所使用的对齐方法。	3  
> text-outline	规定文本的轮廓。	3  
> text-overflow	规定当文本溢出包含元素时发生的事情。	3  
> text-shadow	向文本添加阴影。	3  
> text-wrap	规定文本的换行规则。	3  
> word-break	规定非中日韩文本的换行规则。	3  
> word-wrap	允许对长的不可分割的单词进行分割并换行到下一行。	3  


#### 字体  
可以将自己喜欢的任意字体上传到 server 并使用  
会被自动下载到用户浏览器  
```  
@font-face {
  font-family: myFirstFont; /* 字体名字 */
  src: url('Console.ttf'),  /* 引用位置 */
       url('Console.ttf');
}
div {
  font-family: myFirstFont; /* 名字引用 */  
}
```  


#### 2D 转换  
1. translate  
当前位置移动 x, y  

2. rotate  
顺时针旋转角度  

3. scale  
元素尺寸的变化  

4. skew  
元素翻转  

5. matrix  
将上述方法整合  


#### 3D 转换  
transform 属性  
1. rotateX  

2. rotateY  

3. 2D Transform  


#### 过渡转化  
transition  
* 应用目标  
* 效果时常  
```  
div{
    transition: width 2s;
    -moz-transition: width 2s;	/* Firefox 4 */
    -webkit-transition: width 2s;	/* Safari 和 Chrome */
    -o-transition: width 2s;	/* Opera */
}
```  


#### 动画  
`@keyframes` 规则  
@keyframes 规则用于创建动画。在 @keyframes 中规定某项 CSS 样式，就能创建由当前样式逐渐改为新样式的动画效果。

使用 @keyframes 创建动画时:  
* 规定动画名称  
* 规定动画时长  
```  
@keyframes myfirst{
    from {background: red;}
    to {background: yellow;}
}
@-moz-keyframes myfirst /* Firefox */{
    from {background: red;}
    to {background: yellow;}
}
@-webkit-keyframes myfirst /* Safari 和 Chrome */ {
    from {background: red;}
    to {background: yellow;}
}
@-o-keyframes myfirst /* Opera */{
    from {background: red;}
    to {background: yellow;}
}
div {
    animation: myfirst 5s;
    -moz-animation: myfirst 5s;	/* Firefox */
    -webkit-animation: myfirst 5s;	/* Safari 和 Chrome */
    -o-animation: myfirst 5s;	/* Opera */
}
```  
[更多参考](http://www.w3school.com.cn/css3/css3_animation.asp)  


#### 多列  
`column-count` `column-gap` `column-gap`  

#### 用户界面  
1. resizing  
是否可由用户调整大小  

2. box-sizing  
以确切的方式定义某个区域的具体内容  

3. outline-offset  
偏移轮廓  
**和边框的区别**  
> 轮廓不占用空间  
> 轮廓可能是非矩形  


