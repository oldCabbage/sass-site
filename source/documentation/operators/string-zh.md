---
title: 字符串运算符
introduction: >
  Sass 支持一些生成 [字符串](/documentation/values/strings) 的运算符：
---

* `<expression> + <expression>` 返回一个包含两个表达式值的字符串。如果任一值是 [带引号的字符串][], 结果将是带引号的；否则，将是不带引号的。

* `<expression> - <expression>` 返回一个包含两个表达式值的不带引号的字符串，两个值之间用 `-` 分隔。这是一个遗留运算符，通常应使用 [插值][] 代替。

[quoted string]: /documentation/values/strings#quoted
[interpolation]: /documentation/interpolation

{% codeExample 'string', false %}
  @debug "Helvetica" + " Neue"; // "Helvetica Neue"
  @debug sans- + serif; // sans-serif
  @debug sans - serif; // sans-serif
  ===
  @debug "Helvetica" + " Neue"  // "Helvetica Neue"
  @debug sans- + serif  // sans-serif
  @debug sans - serif  // sans-serif
{% endcodeExample %}

这些运算符不仅适用于字符串！它们可以用于任何可以写入 CSS 的值，有少数例外：

* 数字不能作为左操作数，因为它们有 [自己的运算符][numeric]。
* 颜色不能作为左操作数，因为它们曾经有 [自己的运算符][color]。

[numeric]: /documentation/operators/numeric
[color]: /documentation/operators

{% codeExample 'string-exceptions', false %}
  @debug "Elapsed time: " + 10s; // "Elapsed time: 10s";
  @debug true + " is a boolean value"; // "true is a boolean value";
  ===
  @debug "Elapsed time: " + 10s  // "Elapsed time: 10s";
  @debug true + " is a boolean value"  // "true is a boolean value";
{% endcodeExample %}

{% headsUp %}
  使用 [插值][] 创建字符串通常比依赖这些运算符更清晰。

  [interpolation]: /documentation/interpolation
{% endheadsUp %}

## 一元运算符

出于历史原因，Sass 也支持 `/` 和 `-` 作为一元运算符，它们只接受一个值：

* `/<expression>` 返回一个以 `/` 开头并跟随表达式值的不带引号的字符串。
* `-<expression>` 返回一个以 `-` 开头并跟随表达式值的不带引号的字符串。

{% codeExample 'unary-operators', false %}
  @debug / 15px; // /15px
  @debug - moz; // -moz
  ===
  @debug / 15px  // /15px
  @debug - moz  // -moz
{% endcodeExample %}