# Typescript
##  基本类型
### 定义方法:
1. const/let/var variable: varType = value;
2. const/let/var array: itemType[] = values;
3. const/let/var tuple: [tupleType1, tupleType2, ... ]; tuple = [value1, value2];
4. enum Color {Red[=val], Green, Blue}; let c: Color = Color.Green;
5. const/let/var noSure: any = value;
6. const/let/var variable: void = undefined/null;

### assertion
*like type cast*
1. let strLength: number = (<string>someValue).length;
2. let strLength: number = (someValue as string).length;

## let 和 var 以及 const
*let 不会泄露到外层*
*声明之前不可用*
*无法重复声明*
*const 用于声明常量*


### 解构
{a, b} = {a: "bac", b: 101};
[a, b] = [1, 2];
let {a: newName1, b: newName2} = o;
默认值
```
function keepWholeObject(wholeObject: {a: string, b?: number}) {
    let {a, b = 1001} = wholeObject;
}
```
*函数传参依旧适用*

## Interface  
*type-checking focuses on the shape that values have*   
*interfaces fill the role of naming these types, and are a powerful way of defining contracts within your code as well as contracts with code outside of your project.*  

### option bags  
```  
interface SquareConfig {
    color?: string;
    width?: number;
}  
```  
* may exist attribute pre-define  
* error when ref un-exist attribute  

### ReadOnly attribute  
```  
interface Point {
    readonly x: number;
    readonly y: number;
}

ReadonlyArray<T> array = [1, 2, 3, 4];  
```  
* variable -> const, attribute -> readonly  

### extra check  
*对象字面量会被特殊对待而且会经过额外属性检查，当将它们赋值给变量或作为参数传递的时候。 如果一个对象字面量存在任何“目标类型”不包含的属性时，你会得到一个错误*  
skip this using type assertion
```  
let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig);  
```  
but best way:  
字符串索引签名  
```  
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```  
strange way:  
```  
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```  

### 函数类型  
```  
interface SearchFunc {
  (source: string, subString: string): boolean;
}


let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  let result = source.search(subString);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```  
*参数名不需要相同*  

### 可索引类型  
*可索引类型具有一个索引签名，它描述了对象索引的类型，还有相应的索引返回值类型*  
```  
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```  
*共有支持两种索引签名：字符串和数字。 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。*  
*字符串索引签名能够很好的描述dictionary模式，并且它们也会确保所有属性与其返回值类型相匹配。*  

### 类类型  
```  
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```  
*接口描述了类的公共部分，而不是公共和私有两部分。 它不会帮你检查类是否具有某些私有成员。*  

### 类静态部分与实例部分的区别  
```  
// Error
interface ClockConstructor {
    new (hour: number, minute: number);
}

class Clock implements ClockConstructor {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
// 这里因为当一个类实现了一个接口时，只对其实例部分进行类型检查。 constructor存在于类的静态部分，所以不在检查的范围内。
```  

### 扩展接口  
和类一样，接口也可以相互扩展。 这让我们能够从一个接口里复制成员到另一个接口里，可以更灵活地将接口分割到可重用的模块里。  
```  
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```  

### 混合类型  
```  
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```  

### 接口继承类  
当接口继承了一个类类型时，它会继承类的成员但不包括其实现。 就好像接口声明了所有类中存在的成员，但并没有提供具体实现一样。 接口同样会继承到类的private和protected成员。 这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现（implement）。
```  
class Control {
    private state: any;
}

interface SelectableControl extends Control {
    select(): void;
}

class Button extends Control {
    select() { }
}
class TextBox extends Control {
    select() { }
}
class Image extends Control {
}
class Location {
    select() { }
}
```  





