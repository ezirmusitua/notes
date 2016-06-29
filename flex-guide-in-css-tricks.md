## Flexbox Layout  
一种更有效率的布局/对齐和空间分配方式  
背后思想: 给予容器控制其内部元素 size 的能力  
和传统布局方法相比，是方向无关的  

### 基础 & 基本术语 -- container 属性 
1. container  
![container](https://css-tricks.com/wp-content/uploads/2014/05/flex-container.svg)  
```  
.container {
  display: flex;
}
```  
2. flex-direction  
![flex-direction](https://css-tricks.com/wp-content/uploads/2014/05/flex-direction1.svg)  
```  
.container {
  display: flex;
  flex-direction: row | row-reverse | column | column-reverse;
}
```  
3. flex-wrap  
![flex-wrap](https://css-tricks.com/wp-content/uploads/2014/05/flex-wrap.svg)  
```  
.container {
  display: flex;
  flex-direction: row | row-reverse | column | column-reverse;
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```  
4. flex-flow  
**Applies to: parent flex container element**  
**shorthand for `flex-direction` and `flex-wrap`**  
```  
flex-flow: <‘flex-direction’> || <‘flex-wrap’>  
```  
5. justify-content  
alignment along the main axis  
![justify-content](https://css-tricks.com/wp-content/uploads/2013/04/justify-content.svg)  
```  
.container {
    display: flex;
    flex-direction: row;
    justify-content: flex-start | flex-end | center | space-between | space-around;
}  
```  
6. align-content  
This aligns a flex container's lines within when there is extra space in the cross-axis  
![align-content](https://css-tricks.com/wp-content/uploads/2014/05/align-items.svg)
```  
.container {
    display: flex;
    flex-direction: row;
    align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}  
```  

### 基础 & 基本术语 -- items 属性 
1. items  
![items](https://css-tricks.com/wp-content/uploads/2014/05/flex-items.svg)  
2. order  
control order.  
![order](https://css-tricks.com/wp-content/uploads/2013/04/order-2.svg)  
```  
.item {
    order: <integer>;  
}  
```  
3. flex-grow  
Defines the ability for a flex item to grow if necessary
![flex-grow](https://css-tricks.com/wp-content/uploads/2014/05/flex-grow.svg)  
```  
.item {
    flex-grow: <number>;  
}  
```  
4. flex-shrink  
Defines the ability for a flex item to shrink if necessary
```  
.item {
    flex-shrink: <number>;
}  
```  

5. flex-basis  
Defines the default size of an element before the remaining space is distributed
```  
.item {
    flex-basis: <length> | auto;  
}  
```  

6. flex  
This is the shorthand for flex-grow, flex-shrink and flex-basis combined  
```  
.item {
    flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```  
**It is recommended that you use this shorthand property**  

7. align-self  
This allows the default alignment (or the one specified by align-items) to be overridden for individual flex items  
![align-self](https://css-tricks.com/wp-content/uploads/2014/05/align-self.svg)  
```  
.item {  
    align-self: auto | flex-start | flex-end | center | baseline | stretch;  
}  
```  
**`float`, `clear` and `vertical-align` have no effect on a flex item**



