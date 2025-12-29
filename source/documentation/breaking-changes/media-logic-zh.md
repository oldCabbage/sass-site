---
title: '重大变更: Media Queries Level 4'
introduction: >
  Sass 已经添加了对 CSS Media Queries Level 4 规范的支持。这最初与一些 Sass 特定的语法冲突，因此该语法已被弃用，并且现在根据 CSS 标准进行解释。
---

{% compatibility 'dart: "1.56.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

因为 Sass 支持几乎任何 Sass 表达式作为括号内的媒体条件，所以在添加对 Media Queries Level 4 的完整支持后，某些结构的含义发生了变化。具体来说：

- `@media (not (foo))` 在历史上被 Sass 解释为 `@media (#{not (foo)})`，因此编译为 `@media (false)`。

- `@media ((foo) and (bar))` 和 `@media ((foo) or (bar))` 同样被解释为 SassScript 的逻辑运算符，分别编译为 `@media (bar)` 和 `@media (foo)`。

幸运的是，这些情况在实践中很少出现。

## 过渡期

{% compatibility 'dart: "1.54.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

首先，我们为之前的歧义情况发出了弃用警告。这些警告会提供如何保留现有行为或如何使用新的 CSS 语法的建议。

{% render 'silencing_deprecations' %}