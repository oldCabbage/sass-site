---
title: 布尔运算符
introduction: >
  与 JavaScript 等语言不同，Sass 使用单词而不是符号来表示其 [布尔值](/documentation/values/booleans) 运算符。
---

* `not <expression>` 返回表达式的相反值：将 `true` 转换为 `false`，将 `false` 转换为 `true`。
* `<expression> and <expression>` 如果两个表达式的值均为 `true`，则返回 `true`；如果其中任何一个为 `false`，则返回 `false`。
* `<expression> or <expression>` 如果两个表达式中的任何一个值为 `true`，则返回 `true`；如果两个都为 `false`，则返回 `false`。

{% codeExample 'boolean', false %}
  @debug not true; // false
  @debug not false; // true

  @debug true and true; // true
  @debug true and false; // false

  @debug true or false; // true
  @debug false or false; // false
  ===
  @debug not true  // false
  @debug not false  // true

  @debug true and true  // true
  @debug true and false  // false

  @debug true or false  // true
  @debug false or false  // false
{% endcodeExample %}

{% render 'doc_snippets/truthiness-and-falsiness' %}