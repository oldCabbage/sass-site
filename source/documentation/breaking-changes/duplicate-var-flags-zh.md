---
title: '重大变更：重复变量标志'
introduction: >
  变量将只允许单个 `!global` 或 `!default` 标志。重复的标志从未有过任何额外的效果，这只是确保样式表更加一致。
---

## 第一阶段

{% compatibility 'dart: "2.0.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

从 Dart Sass 2.0.0 开始，如果一个变量声明包含多个 `!global` 或 `!default` 标志，这将是一个语法错误。这意味着 `$var: value !default !default` 将被禁止。`$var: value !global !default` 仍然被允许。

## 过渡期

{% compatibility 'dart: "1.62.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

在 Dart Sass 2.0.0 发布之前，多个标志的副本只会产生弃用警告。

{% render 'silencing_deprecations' %}