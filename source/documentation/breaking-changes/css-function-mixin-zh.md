---
title: "重大变更：以 -- 开头的函数和混合宏"
introduction: |
  在 Dart Sass 1.76.0 之前，函数和混合宏的名称可以是任何有效的 CSS
  标识符，但以 `--` 开头的标识符现在已被弃用。
---

通常，Sass 允许任何有效的 CSS 标识符用于任何 Sass
定义。这包括以 `--` 开头的标识符，用户可能在 [CSS 自定义属性] 的上下文中最熟悉这些标识符。然而，CSS
工作组正在 [认真考虑] 为 CSS 本身添加对函数和混合宏的内置支持，可能使用类似于 Sass 的 `@mixin` 和 `@function` 命名的 at-rules。

[CSS 自定义属性]: https://www.w3.org/TR/css-variables-1/
[认真考虑]: https://github.com/w3c/csswg-drafts/issues/9350

这意味着 Sass 为了在保持其核心设计原则 CSS 兼容性的同时仍然支持 Sass 的构建时函数和混合宏，需要能够区分使用相同 at-rules 名称的 CSS 和 Sass 声明。幸运的是，尽管 CSS 用于函数和混合宏的语法细节仍然悬而未决，但有一点似乎是无争议的：使用类似于自定义属性的以 `--` 开头的标识符作为 CSS 混合宏和函数名称。

这将允许 Sass 将以 `--` 开头的函数和混合宏识别为纯 CSS 的。但是为了使这起作用，我们首先必须禁止 Sass 函数和混合宏使用该前缀。

## 过渡期

### 第一阶段：弃用

{% compatibility 'dart: "1.76.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

在 Dart Sass 1.76.0 和 Dart Sass 1.94.0 之间，Dart Sass 继续允许名称以 `--` 开头的函数和混合宏。然而，它会发出一个名为 `css-function-mixin` 的弃用警告。

### 第二阶段：纯 CSS 函数

{% compatibility 'dart: "1.94.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

Dart 1.94.0 添加了对纯 CSS 函数的支持，因为它们在不久之前已登陆 Chrome。任何名称以 `--` 开头的函数现在都会被解析为纯 CSS 的 at-rule，其 `result` 属性的解析方式与自定义属性值相同。

在第二阶段，名称以 `--` 开头的混合宏从弃用升级为错误。

{% render 'silencing_deprecations' %}