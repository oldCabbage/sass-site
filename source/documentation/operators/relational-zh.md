---
title: 关系运算符
introduction: >
  关系运算符用于确定
  [数字](/documentation/values/numbers)之间的大小关系。它们会自动在兼容的单位之间进行转换。
---

* `<expression> < <expression>` 返回第一个 [expression][] 的值是否小于第二个值。
* `<expression> <= <expression>` 返回第一个 [expression][] 的值是否小于或等于第二个值。
* `<expression> > <expression>` 返回第一个 [expression][] 的值是否大于第二个值。
* `<expression> >= <expression>`，返回第一个 [expression][] 的值是否大于或等于第二个值。

[expression]: /documentation/syntax/structure#expressions

{% codeExample 'relational', false %}
  @debug 100 > 50; // true
  @debug 10px < 17px; // true
  @debug 96px >= 1in; // true
  @debug 1000ms <= 1s; // true
  ===
  @debug 100 > 50  // true
  @debug 10px < 17px  // true
  @debug 96px >= 1in  // true
  @debug 1000ms <= 1s  // true
{% endcodeExample %}

无单位的数字可以与任何数字进行比较。它们会自动转换为该数字的单位。

{% codeExample 'unitless-numbers', false %}
  @debug 100 > 50px; // true
  @debug 10px < 17; // true
  ===
  @debug 100 > 50px  // true
  @debug 10px < 17  // true
{% endcodeExample %}

具有不兼容单位的数字不能进行比较。

{% codeExample 'incompatible-units', false %}
  @debug 100px > 10s;
  //     ^^^^^^^^^^^
  // Error: Incompatible units px and s.
  ===
  @debug 100px > 10s
  //     ^^^^^^^^^^^
  // Error: Incompatible units px and s.
{% endcodeExample %}