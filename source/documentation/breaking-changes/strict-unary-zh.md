---
title: '重大变更：严格的单目运算符'
introduction: >
  Sass 历史上允许 `-` 和 `+` 以一种方式使用，使得作者是否希望它们作为二元或单目运算符意图不明确。
  这种令人困惑的语法已被弃用。
---

这个属性是如何编译的？

{% codeExample 'strict-unary', false %}
  $size: 10px;

  div {
    margin: 15px -$size;
  }
  ===
  $size: 10px

  div
    margin: 15px -$size
{% endcodeExample %}

一些用户可能会说“`-` 附着在 `$size` 上，所以应该是 `margin: 20px -10px`”。另一些用户可能会说“`-` 在 `20px` 和 `$size` 之间，所以应该是 `margin: 5px`”。Sass 目前同意后一种意见，但真正的问题是它一开始就是如此令人困惑！这是 CSS 的空格分隔列表语法与 Sass 的算术语法结合的自然但不幸的结果。

因此，我们将把这个改为错误。在未来，如果你想使用二元 `-` 或 `+` 运算符（即，减去或加上两个数字），你需要在两边都加空格或者两边都不加空格：

* 有效：`15px - $size`
* 有效：`(15px)-$size`
* 无效：`15px -$size`

如果你想在一个空格分隔的列表中使用单目 `-` 或 `+` 运算符，你（仍然）需要将其括在括号中：

* 有效：`15px (-$size)`

## 过渡期

{% compatibility 'dart: "1.55.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

我们将在 Dart Sass 2.0.0 中把这个改为错误，但在那之前它只会发出弃用警告。

{% render 'doc_snippets/silence-deprecations' %}

## 自动迁移

你可以使用 [the Sass migrator] 自动更新你的样式表，在任何需要的 `-` 或 `+` 运算符后面添加空格，这将保留这些样式表的现有行为。

[the Sass migrator]: https://github.com/sass/migrator#readme

```shellsession
$ npm install -g sass-migrator
$ sass-migrator strict-unary **/*.scss
```

{% render 'silencing_deprecations' %}