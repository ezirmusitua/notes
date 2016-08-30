## 封装  
function ->  
constructor -> 
prototype  

```  
// 将不变的属性和方法定义在 prototype 上  
function Cat(name,color){
    this.name = name;
　　this.color = color;
}
Cat.prototype.type = "猫科动物";
Cat.prototype.eat = function(){alert("吃老鼠")};  
var cat1 = new Cat("hello", "yellow");
```  

验证方法:  
1. isPrototype  
2. hasOwnProperty  
3. in 

## 继承  
1. 构造函数绑定  
```  
Parent.apply(this, arguments);
```  

2. prototype 模式  
```  
Cat.prototype = new Animal();
// 必须手动纠正继承链
Cat.prototype.constructor = Cat;
var cat1 = new Cat("大毛","黄色");
alert(cat1.species); // 动物
```  

3. 直接继承 prototype  
```  
// 任何对 Cat.prototype 的修改，都会反映到 Animal.prototype
Cat.prototype = Animal.prototype;
Cat.prototype.constructor = Cat;
var cat1 = new Cat("大毛","黄色");
alert(cat1.species); // 动物
```

4. {} 为中介  
```  
function extend(Child, Parent) {

    var F = function () {};
    F.prototype = Parent.prototype;
    Child.prototype = new F();
    Child.prototype.constructor = Child;
    Child.uber = Parent.prototype;
}

5. 拷贝继承  
```  
function extend2(Child, Parent) {
    var p = Parent.prototype;
    var c = Child.prototype;
    for (var i in p) {
    　　c[i] = p[i];
    　　}
    c.uber = p;
}
```  


## 非构造函数的继承  
**更像是组合**  
深拷贝:  
```  
function deepCopy(p, c) {
    var c = c || {};
    for (var i in p) {
        if (typeof p[i] === 'object') {
            c[i] = (p[i].constructor === Array) ? [] : {};
            deepCopy(p[i], c[i]);
        } else {
            c[i] = p[i];
        }
    }
    return c;
}
```  

