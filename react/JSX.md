# JSX

## 概念:

JSX是JavaScript和XML (HTML)的缩写，表示在<u>JS代码中编写HTML模版</u>结构,它是React中编写
UI模版的方式

```JSX
function App() {
  return (
    <div className="App">
        hello
    </div>
  );
}
```



### JSX的本质
JSX并不是标准的JS语法，它是<u>JS的语法扩展</u>，浏览器本身不能识别，需要通过<u>解析工具做解析</u>之后才能在浏览器中运行

所以需要使用工具例如`Bable`将`JSX`转化为`JS`

```
<div>
</div>
```

JS:

```javascript
import { jsx as  _jsx ] from "react/jsx-runtime";

/*# PURE */ 
_jsx("div"，{
        children:"this is div"
});

```

## JSX中使用JS表达式

在JSX中可以通过大括号{}识别Javascript表达式

1.使用引号传递字符串
2.使用JavaScript变量
3.函数调用和方法调用
4.使用JavaScript对象

注意: if语句、switch语句、变量声明属于语句，不是表达式，不能出现在0中