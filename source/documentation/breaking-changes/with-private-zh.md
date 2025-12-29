---
title: '重大变更：私有配置'
introduction: >
  当 Sass 引入新的模块系统时，它还引入了仅应在模块内可见或可修改的私有变量的概念。但存在一个意外的漏洞：这些变量仍然可以被配置。
---

可以编写 `@use "module" with ($-private: value)` 来影响模块私有变量的值。这违背了语言的预期行为，也可能违背模块作者的意图。为了关闭这个漏洞，我们将努力使其成为配置私有变量的错误。

我们仍然计划允许使用 `!default` 声明私有变量，因为这仍然是在变量当前为 `null` 时赋值的有用方式（类似于 JavaScript 中的 [`??=` 赋值]）。

[`??=` 赋值]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_assignment

{% headsUp %}
  任何以 `-` 或 `_` 开头的变量都被视为私有变量，包括以 `--` 开头的变量。因此，我们不建议使用 CSS 自定义属性样式的名称作为 Sass 变量名称。
{% endheadsUp %}

### 第一阶段

{% compatibility 'dart: "1.92.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

目前，如果在配置中使用私有变量名称，Dart Sass 会发出弃用警告，但如果模块使用 `!default` 声明该变量，仍然允许配置该变量。

要修复任何违规情况，请将所有打算被配置的变量改为不再私有，删除所有前导的 `-` 或 `_` 字符。

{% render 'silencing_deprecations' %}

### 第二阶段

{% compatibility 'dart: false', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

在 Dart Sass 2.0.0 中，包含私有变量的配置将被视为错误。