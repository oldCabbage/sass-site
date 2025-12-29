---
title: '重大变更：斜杠作为除法'
introduction: >
  Sass 目前在某些上下文中将 `/` 视为除法操作，在其他上下文中将其视为分隔符。这使得 Sass 用户难以确定任何给定的 `/` 的含义，并且难以使用将 `/` 用作分隔符的新 CSS 特性。
---

{% compatibility 'dart: "partial"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

今天，Sass 使用 [复杂启发式][] 来确定 `/` 应该被视为除法还是分隔符。即使如此，作为分隔符时，它也只是生成一个难以从 Sass 内部检查的非引用字符串。随着越来越多的 CSS 特性（如 [CSS Grid][] 和 [新的 `rgb()` 和 `hsl()` 语法][]）使用 `/` 作为分隔符，这越来越让 Sass 用户感到痛苦。

[复杂启发式]: /documentation/operators/numeric#slash-separated-values
[CSS Grid]: https://developer.mozilla.org/en-US/docs/Web/CSS/grid-row
[新的 `rgb()` 和 `hsl()` 语法]: https://drafts.csswg.org/css-color/#rgb-functions

因为 Sass 是 CSS 超集，我们通过重新定义 `/` 仅为分隔符来匹配 CSS 的语法。`/` 将被视为一种新的列表分隔符类型，类似于今天 `,` 的工作方式。除法则将使用新的 `math.div()` 函数来编写。此函数将与 `/` 今天的操作方式完全相同。

此弃用不影响 `calc()` 表达式内的 `/` 使用。

{% codeExample 'slash-div' %}
  @use "sass:math";

  // 未来的 Sass，目前尚不可用！
  .item3 {
    $row: span math.div(6, 2) / 7; // 一个包含两个元素的斜杠分隔列表。
    grid-row: $row;
  }
  ===
  @use "sass:math"

  // 未来的 Sass，目前尚不可用！
  .item3
    $row: span math.div(6, 2) / 7 // 一个包含两个元素的斜杠分隔列表。
    grid-row: $row
  ===
  .item3 {
    grid-row: span 3 / 7;
  }
{% endcodeExample %}

## 过渡期

{% compatibility 'dart: "1.33.0"', 'libsass: false', 'ruby: false', 'feature: "math.div() and list.slash()"' %}{% endcompatibility %}

为了简化过渡，我们首先添加了 `math.div()` 函数。`/` 运算符目前仍然用于除法，但在此过程中会打印弃用警告。用户应将所有除法切换为使用 `math.div()`。

{% render 'doc_snippets/silence-deprecations' %}

{% codeExample 'math-div', false %}
  @use "sass:math";

  // 错误，未来 Sass 版本中将无法使用。
  @debug (12px/4px); // 3

  // 正确，未来 Sass 版本中将可以使用。
  @debug math.div(12px, 4px); // 3
  ===
  @use "sass:math"

  // 错误，未来 Sass 版本中将无法使用。
  @debug (12px/4px) // 3

  // 正确，未来 Sass 版本中将可以使用。
  @debug math.div(12px, 4px) // 3
{% endcodeExample %}

在过渡期内，斜杠分隔列表也将可用。因为目前还不能使用 `/` 来创建它们，将添加 `list.slash()` 函数来创建它们。你还可以将 `"slash"` 作为 `$separator` 参数传递给 [`list.join()` 函数][] 和 [`list.append()` 函数][]。

[`list.join()` 函数]: /documentation/modules/list#join
[`list.append()` 函数]: /documentation/modules/list#append

{% codeExample 'slash-div-list' %}
  @use "sass:list";
  @use "sass:math";

  .item3 {
    $row: list.slash(span math.div(6, 2), 7);
    grid-row: $row;
  }
  ===
  @use "sass:list"
  @use "sass:math"

  .item3
    $row: list.slash(span math.div(6, 2), 7)
    grid-row: $row
{% endcodeExample %}

{% compatibility 'dart: "1.40.0"', 'libsass: false', 'ruby: false', 'feature: "First-class calc"' %}{% endcompatibility %}

或者，用户可以将除法操作包装在 `calc()` 表达式中，Sass 将将其简化为单个数字。

{% codeExample 'slash-div-calc', false %}
  // 错误，未来 Sass 版本中将无法使用。
  @debug (12px/4px); // 3

  // 正确，未来 Sass 版本中将可以使用。
  @debug calc(12px / 4px); // 3
  ===
  // 错误，未来 Sass 版本中将无法使用。
  @debug (12px/4px) // 3

  // 正确，未来 Sass 版本中将可以使用。
  @debug calc(12px / 4px) // 3
{% endcodeExample %}

## 自动迁移

你可以使用 [Sass 迁移工具][] 自动更新样式表以使用 `math.div()` 和 `list.slash()`。

[Sass 迁移工具]: https://github.com/sass/migrator#readme

```shellsession
$ npm install -g sass-migrator
$ sass-migrator division **/*.scss
```

{% render 'silencing_deprecations' %}