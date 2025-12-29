---
title: 样式表的结构
table_of_contents: true
introduction: >
  就像 CSS 一样，大多数 Sass 样式表主要由包含属性声明的样式规则组成。但是 Sass 样式表有许多其他功能可以与这些功能并存。
---

## 语句

Sass 样式表由一系列 _语句_ 组成，这些语句按顺序进行评估以构建最终的 CSS。某些语句可能具有 _块_，使用 `{` 和 `}` 定义，其中包含其他语句。例如，样式规则是一个具有块的语句。该块包含其他语句，如属性声明。

在 SCSS 中，语句用分号分隔（如果语句使用块，则分号是可选的）。在缩进语法中，它们仅由新行分隔。虽然你可以在缩进语法中使用分号结束语句，但仍然需要新行。

### 全局语句

这些类型的语句可以在 Sass 样式表的任何位置使用：

- [变量声明](/documentation/variables)，如 `$var: value`。
- [流程控制 at 规则](/documentation/at-rules/control)，如 `@if` 和 `@each`。
- [`@error`](/documentation/at-rules/error)，[`@warn`](/documentation/at-rules/warn)，和 [`@debug`](/documentation/at-rules/debug) 规则。

### CSS 语句

这些语句会产生 CSS。它们可以用于任何位置，除了 `@function` 内部：

- [样式规则](/documentation/style-rules)，如 `h1 { /* ... */ }`。
- [CSS at 规则](/documentation/at-rules/css)，如 `@media` 和 `@font-face`。
- 使用 `@include` 的 [Mixin 使用](/documentation/at-rules/mixin)。
- [`@at-root` 规则](/documentation/at-rules/at-root)。

### 顶级语句

这些语句只能用于样式表的顶级，或嵌套在顶级的 CSS 语句中：

- 使用 `@use` 的 [模块加载](/documentation/at-rules/use)。
- 使用 `@import` 的 [导入](/documentation/at-rules/import)。
- 使用 `@mixin` 的 [Mixin 定义](/documentation/at-rules/mixin)。
- 使用 `@function` 的 [函数定义](/documentation/at-rules/function)。

### 其他语句

- [属性声明](/documentation/style-rules/declarations) 如 `width: 100px` 只能在样式规则和某些 CSS at 规则中使用。
- [`@extend` 规则](/documentation/at-rules/extend) 只能在样式规则中使用。

## 表达式

_表达式_ 是属性或变量声明右侧的任何内容。每个表达式都会生成一个 _[值][]_。任何有效的 CSS 属性值也是 Sass 表达式，但 Sass 表达式比纯 CSS 值强大得多。它们作为参数传递给 [mixin][] 和 [函数][]，用于控制流的 [`@if` 规则][]，并使用 [算术][] 进行操作。我们称 Sass 的表达式语法为 _SassScript_。

[value]: /documentation/values
[mixins]: /documentation/at-rules/mixin
[functions]: /documentation/at-rules/function
[`@if` 规则]: /documentation/at-rules/control/if
[算术]: /documentation/operators/numeric

### 字面量

最简单的表达式仅表示静态值：

- [数字](/documentation/values/numbers)，可能带有单位，如 `12` 或 `100px`。
- [字符串](/documentation/values/strings)，可能带有引号，如 `"Helvetica Neue"` 或 `bold`。
- [颜色](/documentation/values/colors)，可以通过其十六进制表示或名称引用，如 `#c6538c` 或 `blue`。
- 布尔值字面量 [boolean](/documentation/values/booleans) `true` 或 `false`。
- 单例 [`null`](/documentation/values/null)。
- [值列表](/documentation/values/lists)，可能用空格或逗号分隔，并可能用方括号或不带括号，如 `1.5em 1em 0 2em`，`Helvetica, Arial, sans-serif`，或 `[col1-start]`。
- [映射](/documentation/values/maps)，将值与键关联，如 `("background": red, "foreground": pink)`。

### 操作

Sass 定义了多种操作的语法：

{% render 'doc_snippets/operator-list', parens: true %}

### 其他表达式

- [变量](/documentation/variables)，如 `$var`。
- [函数调用](/documentation/at-rules/function)，如 `nth($list, 1)` 或 `var(--main-bg-color)`，可以调用 Sass 核心库函数或用户定义的函数，也可以直接编译为 CSS。
- [特殊函数](/documentation/syntax/special-functions)，如 `calc(1px + 100%)` 或 `url(http://myapp.com/assets/logo.png)`，具有自己的独特解析规则。
- [父选择器](/documentation/style-rules/parent-selector)，`&`。
- 值 `!important`，解析为无引号的字符串。