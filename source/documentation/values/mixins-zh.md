---
title: 混合值
---

{% compatibility 'dart: "1.69.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

[混合宏] 也可以作为值！你不能直接将混合宏写成一个值，但你可以将混合宏的名称传递给 [`meta.get-mixin()` 函数] 以获取其值。一旦你有了混合宏的值，你可以将其传递给 [`meta.apply()` 混合宏] 来调用它。这对于库以复杂且强大的方式实现可扩展性非常有用。

[混合宏]: /documentation/at-rules/mixin
[`meta.get-mixin()` 函数]: /documentation/modules/meta#get-mixin
[`meta.apply()` 混合宏]: /documentation/modules/meta#apply

{% render 'code_snippets/example-first-class-mixin' %}