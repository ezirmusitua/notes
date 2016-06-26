# 概述  
## JavaScript语言特性  
动态性: 为属性赋值不必事先创建字段, 使用时赋值即可  
弱类型: 声明时无须指定类型, 解释器根据上下文实例化  
**类型和值关联**  
面向对象: 在JavaScript中一切都是对象  
解释编译: 解释型语言  

- - -  
# 基本概念  
## 数据类型  
string, number, boolean, undefined, null, object  
对象类型:  
复合型类型  
> 对象(属性集合)  
> 数组(有序列表)  
> 函数(可执行)  

类型转换:  
> 自动转换  
> 对象->基本: valueOf,toString  

类型判断:
`typeof`  
`instanceof`  

## 变量  
变量: 对值存储空间的引用, 通过一个名字将值关联起来  
基本类型: 直接引用数值  
引用类型: 引用对象自身  

变量的作用域: 函数内必须声明var, 否则为全局  

## 运算符  
\[\]: 下标取值  
.   : 属性取值  
\=\= & \=\=\= : \=\=会进行类型转换, \=\=\=不会  

- - -  
# 对象  
## JavaScript对象  
属性: 字符串, .&\[\]  
**变量其实就是属性**  
**原型和原型链**  
遍历原型链  
`this`指针: 当前上下文, 对调用者的引用  

## 使用对象  
声明:  
> new  
> 定义类, new类  
> 对象字面量

定义类:  
```  
function Address(stree, xno) {  
    this.street   = street || 'Huang Quan Road';  
    this.xno      = xno    || 135;  
    this.toString = function () {
        return "street: " + this.street + ", No: " + this.xno;  
    }  
}  
```  
new 类:  
```  
address = new Address("Hell", 444);  
```  

## 对象字面量  
示例:  
```  
var obj = {  
    attrib1 : 1,  
    attrib2 : 2,  
    attrib3 : 3,  
    method1 : function () {  
        // do something  
    },  
    // else
}  
```  
示例:  
```  
function point (atr1, atr2) {  
    this.atr1 = atr1 || "none";  
    this.atr2 = atr2 || "none";  
    return {
        x: this.atr1,  
        y: this.atr2  
    }  
}  
var pos = point(1,2);  
```  

- - -  
# 函数  
## 函数对象  
创建函数:  
```  
var add = function (...) { ... }  
function func (...) { ... }  
var func = new Function (params, body);  
```  
**可以传递任意多个参数**  
参数列表: `arguments`  

## 函数作用域  
词法作用域:  
作用域在定义时确定, 不在运行时确定  
**变量作用域在函数体内, 无块作用域**  
**所有函数之外的声明为全局变量**  
**查找变量: 遍历作用域链**  

## 函数上下文  
**函数自身是独立的**  

## call和apply  
用于修改函数上下文  
function.apply(context, \[value\]);  
function.call(context, value);  

- - -  
# 数组  
## 数组特性  
**利用length进行裁剪**  

## 使用数组  
创建:  
new Array();  
new Array(10);  
new Array(value, value);  
var arr = [value, ...\]  

删除:  
无delete/remove方法, 自定义  

遍历:  
```  
for (var item in array) {  
    //do ...  
}  
```  

- - -  
# 正则表达式  
## 使用正则表达式  
创建:  
```  
var pattern = /pattern/[switchs\]  
pattern.text()/exec()/compile()  
```  
String中:  
match()/replace()/split()/search()  

- - -  
# 闭包  
由于在JS中, 函数是对象, 对象是属性的集合, 而属性的值又可以是对象, 则在函数内定义函数是理所当然的, 如果在函数func内声明函数inner, 然后再函数外部调用inner, 这个过程产生了闭包  
## 闭包的特性  
**闭包允许引用存在于外部函数中的变量, 然而它并不是使用该变量创建时的值, 而是外部函数中该变量最后的值**  

## 闭包的用途  
1. 匿名自执行函数  
只执行一次, 内部变量无需维护 -- UI初始化  
2. 缓存  
不释放外部引用  
3. 封装的实现  
**实现OOP中的对象**  

## 应注意的问题  
**内存泄漏**  
`A->B->C->A`  
上下文引用问题  

- - -  
# 面向对象的JavaScript  
## 原型继承  
继承在JS中可以通过原型链实现  
查找顺序:  
`目标对象->原型链`  
**JS中引用始终指向最终对象, 而非引用本身**  
new操作:  
new Function()  
> create {}  
> function.apply(context, {})  

- - -  
# 函数式JavaScript  
## 匿名函数  
没有名字的函数  

## 高阶函数  
以一个或多个函数为参数的函数  

## 闭包与柯里化  
柯里化:  
预先将函数的某些参数传入, 得到一个简单的函数, 但是预先传入的参数被保存在闭包中  
```  
var adder = function (num) {  
    return function (y) {  
        return num + y;  
    }
}
var inc = adder(1);  
var dec = adder(-1);  
```  
`Y-结合因子`  

- - -  
# 核心概念深入  
## 原型链  
对象是属性的集合(包含一个隐含属性 -- 原型)  
原型也是对象  

构造器:  
```  
function Task(id) {  
    this.id = id;  
}  
Task.prototype.status = "STOPPED";  
Task.prototype.execute = function (args) {
    ... 
}  
var task = new Task(1);  
```  

## 执行期上下文  
**运行期概念**  
**栈结构**  

## 活动对象  
**函数被调用时产生**  
包含若干属性  

## 作用域链  
对象组成的链, 用以在上下文中查找标识符, 查找方式类似原型链  

## this值  
特殊对象, 与执行器上下文相关, 上下文对象  

- - -  
# 客户端的JavaScript  
## 客户端JavaScript执行环境  
window

## DOM  
Document Object Model

## 事件驱动模型  
**代码块的运行与否和程序流程无关**  
**面向用户**  

- - -  
# 服务器端JavaScript  
## node.js  
基于事件,非阻塞I/O框架  
通过注册时间处理器确保当关心的事件发生时进行回调  
间接层: 提高并发, 耗时操作作为独立进程  

- - -  
# 附录A: 一些JavaScript技巧  
## 创建对象  
使用字面值常量  

## 访问对象属性  
eval(string)  

## 遍历对象  
数组: for (var i = 0; i < array.length; i++)  
对象: for (var item in obj)  

## 名称空间  
属性嵌套, 如何用字符串实现?  


# 最后:  
**省略了很多内容, 被省略的内容可能是不感兴趣, 或者冗余阅读(了解/未来会学)**  