---
title: '重大变更: meta.feature-exists()'
introduction: >
  `meta.feature-exists()` 函数已经很长时间没有添加任何新功能，现在已被弃用。用户应使用其他方法来确定新功能是否可用。
---

历史上，Sass 使用 `meta.feature-exists()` 函数（也可作为全局 `feature-exists()` 函数使用）来允许作者检测编译样式表时各种新语言功能是否可用。然而，随着时间的推移，发现大多数新 Sass 功能要么可以通过更直接的方式检测到，要么根本不值得检测。

此函数现在已被弃用，并将在 Dart Sass 2.0.0 中移除。由于 Dart Sass 现在是唯一官方支持的 Sass 实现，并且所有 Dart Sass 版本都支持 `meta.feature-exists()` 支持的所有功能，因此所有现有的使用都可以安全地移除。

许多新功能可以使用 [`meta.function-exists()`]、[`meta.mixin-exists()`] 或 [`meta.global-variable-exists()`] 进行检测。其他功能可以通过表达式级别的语法进行检测，例如使用 `calc(1) == 1` 来确定当前版本的 Sass 是否支持一级计算。

[`meta.function-exists()`]: /documentation/modules/meta#function-exists
[`meta.mixin-exists()`]: /documentation/modules/meta#mixin-exists
[`meta.variable-exists()`]: /documentation/modules/meta#variable-exists

## 过渡期

{% compatibility 'dart: "1.78.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

首先，我们将为 `feature-exists` 的所有用法发出弃用警告。

在 Dart Sass 2.0.0 中，`meta.feature-exists()` 将不再存在。尝试调用它将抛出错误，并且尝试调用全局 `feature-exists()` 函数将被视为普通的 CSS 函数调用。

{% render 'silencing_deprecations' %}