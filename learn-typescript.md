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



