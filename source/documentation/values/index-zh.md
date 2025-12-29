---
title: 值
introduction: >
  Sass 支持多种值类型，其中大多数直接来自 CSS。
  每个 [表达式](/documentation/syntax/structure#expressions) 都会产生一个
  值，[变量](/documentation/variables) 保存值。大多数值类型
  直接来自 CSS：
---

* [数字](/documentation/values/numbers)，可能带有单位，也可能不带，例如 `12` 或 `100px`。

* [字符串](/documentation/values/strings)，可能带有引号，也可能不带，例如 `"Helvetica Neue"` 或 `bold`。

* [颜色](/documentation/values/colors)，可以通过它们的十六进制表示或名称引用，例如 `#c6538c` 或 `blue`，或者从函数返回，例如 `rgb(107, 113, 127)` 或 `hsl(210, 100%, 20%)`。

* [值列表](/documentation/values/lists)，可能由空格或逗号分隔，并且可能用方括号括起来或根本不用括号，例如 `1.5em 1em 0 2em`，`Helvetica, Arial, sans-serif`，或
  `[col1-start]`。

还有一些特定于 Sass 的：

* 布尔值 [boolean](/documentation/values/booleans) `true` 和 `false`。

* 单例值 [`null`](/documentation/values/null)。

* [映射](/documentation/values/maps)，将值与键关联，例如
  `("background": red, "foreground": pink)`。

* 由 [`get-function()`][] 返回的 [函数引用](/documentation/values/functions)，并使用 [`call()`][] 调用。

  [`get-function()`]: /documentation/modules/meta#get-function
  [`call()`]: /documentation/modules/meta#call
