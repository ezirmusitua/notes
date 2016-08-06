1. 创建 App 组件 
```  
@Component({
    selector: 'app',
    template: `<div> Hello world!</div>`  
})  
class App{}  

bootstrap(App);
```  

2. 新的组件  
```  
import {TodoInput} from "./todo-input";  

@Component({
    selector: 'app',
    directives: [TodoInput],
    tempalte: `<div><todo-input></todo-input></div>`  
})  

class App{}  

bootstrap(App);  
```  

3. event 监听以及 #ref  
```  
@Component({
    selector: 'todo-input',
    template: `<div>
    <input type="text" #myInput>
    <button (click)="onClick(myInput.value)">Click me</button>
    </div>`  
})  

export class TodoInput {
    onlick(value) {
        console.log(value);  
    }
}
```  

4. Inject Services  
```  
// services  
import {Injectable} from "angular2/core";
@Injectable()
export class TodoService {
    todos = [];
}

// App  
// import service
bootstrap(App, [TodoService])  

// Component  
// import services  
// use service  
export class TodoInput {
    constructor (public todoService: TodoService) {
        // do something
    }
    onClick (value) {
        this.todoService.todos.push(value);
        console.log(this.todoService.todos);
    }
}
```  

5. Inject decorator  
不想用 Typescript 注入 Service 的时候可以使用
```  
export class TodoInput {
    constructor (@Inject(TodoService) public todoService) {
        // do something
    }
    onClick (value) {
        this.todoService.todos.push(value);
        console.log(this.todoService.todos);
    }
}
```  

6. ngFor 循环   
*\*表示可以在模板中使用的 ng directive*  
```  
@Component({
    selector: 'todo-list',
    template: `<div>
        <ul>
            <li *ngFor="#todo of todoService.todos">{{ todo }}</li>
        </ul>
    </div>`
})
```  

7. 双向绑定 -- ng-model
syntax: `[input]` & `(output)`  
```  
@Component({
    selector: 'todo-list',
    template: `<div>
    <input type="text" [(ngModel)]="todoModel">
      {{ todoModel }}  
    <button (click)="onClick()>Click me</button>
    </div>`
})
exports class TodoInput {
    todoModel;

    constructor (public todoService: TodoService) {

    }

    onClick () {
        this.todoService.todos.push(this.todoModel);
        console.log(this.todoService.todos);
    }
```

8. [input] syntax  
使用 [elementProperty] 直接获取`html`的属性  

9. @Input  
@Input 让我们能给在`html`中直接给我们的`controller`和`template`传递属性  
```  
// Component 0  
@Component({
    selector: 'todo-list',
    directives: [TodoItemRenderer],
    template: `<div>
        <ul>
            <li *ngFor="#todo of todoService.todos">
            <todo-item-renderer [todo]="todo"><todo-item-renderer>
            </li>
        </ul>
    </div>`
})    

// Component 1 
@Component({
    selector: 'todo-item-render',
    template: `<div>
    <span [hidden="todo.status == 'completed'">
    {{ todo.title }}
    </span>
    <button (click)="todo.toggle()">Toggle</button>
    </div>`  
})
export class TodoItemRenderer {
    @Input() todo;
}
```  

10. ngClass
`[ngClass]`  
* 可以定义行内样式  
直接根据 Controller 中的值决定使用什么样式  

11. Style 和 View 的封装  
```  
@Component({
    encapsulation: ViewEncapsulation.[None, Native, Default],
    ...  
});
```  

12. filter / pipe  
管道允许我们直接在 Template 中改变值而不会影响到 Controller 中的值  
需要 new #ref   
```  
@Pipe({
    name: 'search'
})

export class SearchPipe {
    transform (value, [status]) {
        return value.filter((item) => item.status === status);
    }
}

```

13. angular2 文件组织  
*按照功能组织，pipes，components，services*

