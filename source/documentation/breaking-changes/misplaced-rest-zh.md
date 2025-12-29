---
title: '重大变更：错误放置的剩余参数'
introduction: >
  Sass 历史上允许剩余参数出现在参数列表的任何位置，尽管它们总是被评估在最后。这关闭了这一漏洞，并要求剩余参数必须出现在参数列表的末尾。
---

{% compatibility 'dart: "1.91.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

Sass 中的剩余参数（写为 `$args...`）始终打算写在参数列表的末尾。它们的评估方式反映了这一点：它们总是运行在所有其他参数之后，并且总是被添加到位置参数列表的末尾。

[Rest arguments]: https://sass-lang.com/documentation/at-rules/function/#taking-arbitrary-arguments

然而，由于 Ruby Sass 和 Dart Sass 实现中的疏忽，这从未被强制执行。可以编写一个函数调用如 `rgb([1, 2]..., 3)`，其中剩余参数位于位置参数（或命名参数）之前。但这并不像看起来的那样工作：它被解析为 `rgb(3, [1, 2]...)` 并导致颜色值 `rgb(3, 1, 2)`。

{% funFact %}
  请注意，在 `@function` 和 `@mixin` 规则的参数声明中，*始终*要求剩余参数出现在末尾。它们不受此弃用的影响。
{% endfunFact %}

为了消除这种混淆，并可能为将来支持剩余参数的更合理行为铺平道路，我们正在分多个阶段进行更改：

### 第一阶段

{% compatibility 'dart: "1.91.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

目前，Dart Sass 在使用剩余参数时，如果不在参数列表的末尾，会发出弃用警告。

要修复任何违规行为并保留现有行为，只需将剩余参数移动到参数列表的末尾。不过，你可能需要检查一下，确保你没有期望它在第一次编写代码时执行与实际不同的操作！

### 第二阶段

{% compatibility 'dart: false', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

在 Dart Sass 2.0.0 中，在参数列表末尾之外使用剩余参数将成为语法错误。

{% render 'silencing_deprecations' %}