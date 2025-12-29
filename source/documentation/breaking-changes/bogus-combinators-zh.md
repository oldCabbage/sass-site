---
title: '重大变更: 无效的组合器'
introduction: >
  Sass 历来对选择器中使用前导、尾随和重复组合器非常宽松。这些组合器将被弃用，除非它们对嵌套有用。
---

Sass 历来支持三种无效的组合器用法：

* 前导组合器，例如 `+ .error {color: red}`。

* 尾随组合器，例如 `.error + {color: red}`。

* 重复组合器，例如 `div > > .error {color: red}`。

这些都不是有效的 CSS，并且所有这些都会导致浏览器忽略相关的样式规则。支持这些组合器增加了 Sass 实现的复杂性，并且使得修复与 `@extend` 规则相关的各种 bug 特别困难。因此，我们 [做出了决定] 要移除对这些用法的支持。

[做出了决定]: https://github.com/sass/sass/issues/3340

**有一个主要的例外**：前导和尾随组合器仍可用于嵌套目的。例如，以下内容仍然被完全支持：

{% codeExample 'bogus-combinators' %}
  .sidebar > {
    .error {
      color: red;
    }
  }
  ===
  .sidebar >
    .error
      color: red
{% endcodeExample %}

只有在嵌套解析后选择器仍然有前导或尾随组合器时，Sass 才会生成错误。另一方面，重复组合器将始终是错误。

为了确保现有样式表中（可能是无意中）包含无效组合器的情况，我们将支持到 Dart Sass 的下一次主要版本发布之前的一个过渡期。

## 过渡期

{% compatibility 'dart: "1.54.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

首先，我们将为所有双组合器发出弃用警告，以及在嵌套解析后最终出现在选择器中的前导或尾随组合器。

{% render 'doc_snippets/silence-deprecations' %}

此外，我们将立即从编译后的 CSS 中省略我们知道是无效 CSS 的选择器，有一个例外：我们 _不会_ 省略以前导组合器开头的选择器，因为它们可能被嵌套的 `@import` 规则或 `meta.load-css()` 混合使用。然而，我们不鼓励这种模式，并将在 Dart Sass 2.0.0 中停止支持它。

{% render 'silencing_deprecations' %}