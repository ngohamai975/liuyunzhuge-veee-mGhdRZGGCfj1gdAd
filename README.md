# JavaScript 自定义元素类的作用域跨环境兼容管理

原创 夏群林 2025.10.22

自定义元素类，是为了后续复用，通常需要全局可见。

JavaScript 类名遵循标识符规范，可包含字母、数字、下划线（\_）、美元符号（$），且不能以数字开头。社区的惯例采用帕斯卡命名法（Pascal Case）。

而按照 Web Components 标准，HTML 自定义元素标签名，必须包含连字符（-），例如 `sudoku-switch`。这是为了与 HTML 内置标签（如 、）区分以避免命名冲突，也确保浏览器能明确识别，从而触发对应的自定义元素实例化逻辑。

自定义元素通过 `customElements.define` 注册。`customElements` 是 `window` 对象的一个属性，属于 Web Components 标准的一部分，在全局作用域中可直接访问，本质上等价于 `window.customElements`。

```
// 定义类
class SudokuSwitch extends HTMLElement {
  // ... 类逻辑 ...
}

// 注册自定义元素
customElements.define('sudoku-switch', SudokuSwitch);
```

`customElements.define('sudoku-switch', SudokuSwitch)` 的作用是：将 `SudokuSwitch` 这个类与自定义标签名 `sudoku-switch` 关联起来，让浏览器知道解析到  标签时，用 `SudokuSwitch` 类来实例化元素。若标签名不含连字符，会直接报错。

通过`customElements.define`注册的前提是：**构造函数（类）在调用时必须处于可访问的作用域**。

但是， ES 模块与非模块环境的作用域隔离规则不同。而且，ES 模块的标识并不需要在所定义的文件头部通过专门的声明语句来体现，而是通过文件的引入方式或运行环境的配置来明确的。在浏览器中，一个 .js 文件是否被视为 ES 模块，由引入它的
