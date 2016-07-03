## Sass  

### 后缀名  
sass 不是使用分号和大括号  
scss 和 css 格式类似  

### 变量  
使用 $ 标识  

#### 声明  
```  
$highlight-color: #fff;
```  
**块中定义块中使用**  
**可以在规则块之外存在**  

#### 引用  
属性标准值可存在的地方就可以使用变量  

中划线和下划线都是可以接受的方式  


### 嵌套的 CSS 规则  
```  
body p {
  background-color: black;
  text-size: 10px;
}  
body h1 {
  background-color: black;
  text-size: 20px;
}
```  
使用嵌套:  
body{
  background-color: black;
  p {
    text-size: 10px;
  }
  h1 {
    text-size: 20px;
  }
}

#### 父选择器标识  
`&`  

#### 群众选择器的嵌套  
**注意嵌套，防止生成的 CSS 太大**  

#### 子组选择器和同层组合选择器: >\+\~  
>: 直接子元素  
+: 同层相邻组合选择器  
~: 同层全体组合选择器  

#### 嵌套属性  
```  
nav {
  border: {
  style: solid;
  width: 1px;
  color: #ccc;
  }
}
```  

### 导入 SASS 文件  
CSS: 
> `@import`: 允许一个在 CSS 文件中导入其他的 CSS 文件，后果是只有执行到 @import 才会去下载 CSS 文件  

SASS:  
> `@import`: 在生成 CSS 时就相关文件导入进来，不需要后缀  

1. 如何编写`被导入`的 SASS 文件   
2. 集中导入 SASS 文件的方法  
3. 如何在 SASS 中使用 CSS 的原生 @import  

#### 使用 SASS 部分文件  
不需要被生成的`局部文件` - 以`下划线`开头，导入时使用绝对名称  

#### 默认变量值  
最后一处覆盖前面的值  
`!default`: 如果`变量`被声明赋值了，就是用声明的值，否则使用默认值  

#### 嵌套导入  
`@import` 允许被写在 CSS 规则内  
被导入的变量和混合器只在规则范围内有效  

#### 原生 CSS 导入  
1. 导入文件以 `.css` 结尾  
2. 导入文件是一个 URL 地址  
3. 被导入文件的名字是 CSS URL() 值  


### 静默注释  
标准注释:  
`/* ... */`  
会出现在生成的 CSS 中  
静默注释:  
`// ... `  
不会出现在生成的 CSS 中  


### 混合器  
代码的重用  
`@mixin` -- 定义一个混合器  
`@include` -- 使用混合器  

#### 何时使用  
存在一组能够用一个名字描述的属性

混合器在样式表中使用  
类名在 HTML 中使用  

#### 混合器中的 CSS 规则  
1. 属性  
2. CSS 规则  
3. 标识符  

**如果一个混合器只包含 CSS 规则而不包含属性，则混合器可以在文档顶部调用  

#### 参数  
例子:  
```  
@mixin link-colors($normal, $hover, $visited) {
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}

a {
  @include link-colors(blue, red, green);
}
```  
键值对传参:  
`$name: value`  

#### 默认参数值  
```  
@mixin link-colors($normal, $hover: $normal, $visited: $normal) {
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
```  


### 使用选择器继承来精简 CSS  
选择器继承指的是一个选择器可以继承为另一个选择器的样式  

#### 何时使用  
当一个元素拥有的类表明它属于另一个类时  

#### 高级用法  
1. 继承 HTML 元素  

#### 集成工作的细节  
1. 继承生成的 CSS 代码相对较少  
2. 继承遵从 CSS 层叠的规则  




