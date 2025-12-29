---
title: '重大变更: type() 函数'
introduction: >
  CSS 添加了一个具有独特语法的 `type()` 函数。为了支持该函数的灵活语法，用户定义的名为 `type()` 的函数将不再允许。
---

CSS Values and Units 5 定义了 [一个 `type()` 函数] 用于 `attr()` 函数中。该函数定义了在将 HTML 属性解析为 CSS 值时使用的语法，因此例如 `attr(data-count type(<number>))` 将返回 `data-count` 属性的值作为 CSS 数字。尽管它目前仅在工作草案中定义，但 Chrome 已经支持 `type()` 函数，因此 Sass 也将添加对该函数的支持。

[一个 `type()` 函数]: https://developer.mozilla.org/en-US/docs/Web/CSS/attr#attr-type

## 废弃

{% compatibility 'dart: "1.86.7"', 'libsass: false', 'ruby: false' %}
{% endcompatibility %}

由于 `type()` 函数不符合 CSS 表达式语法的常规约定，Sass 将需要像 `url()` 或 `element()` 这样的 [特殊函数] 一样解析它。由于这将对任何定义了名为 `type()` 的函数的现有 Sass 代码产生重大变更，Sass 1.86.0 废弃了使用此名称定义函数的能力。

[特殊函数]: /documentation/syntax/special-functions/

{% render 'silencing_deprecations' %}

## 重大变更

{% compatibility 'dart: "1.92.0"', 'libsass: false', 'ruby: false' %}
{% endcompatibility %}

现代版本的 Sass 支持 `type()` 函数作为 [特殊函数]，这意味着它被解析为一种非引用字符串类型，允许使用 `type(<custom-ident>)` 这样的不常见语法。Sass `@function` 规则不再允许定义名为 `type` 的函数。