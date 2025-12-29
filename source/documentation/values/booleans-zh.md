---
title: 布尔值
introduction: >
  布尔值是逻辑值 `true` 和 `false`。除了它们的字面形式外，布尔值还由 [相等性](/documentation/operators/equality)
  和 [关系](/documentation/operators/relational) 运算符返回，以及许多内置函数如
  [`math.comparable()`](/documentation/modules/math#comparable) 和
  [`map.has-key()`](/documentation/modules/map#has-key) 返回。
---

{% codeExample 'booleans', false %}
  @use "sass:math";

  @debug 1px == 2px; // false
  @debug 1px == 1px; // true
  @debug 10px < 3px; // false
  @debug math.comparable(100px, 3in); // true
  ===
  @use "sass:math"

  @debug 1px == 2px  // false
  @debug 1px == 1px  // true
  @debug 10px < 3px  // false
  @debug math.comparable(100px, 3in)  // true
{% endcodeExample %}

你可以使用 [布尔运算符][] 来处理布尔值。`and` 运算符在 *两边* 都为 `true` 时返回 `true`，而 `or` 运算符在 *任一边* 为 `true` 时返回 `true`。`not` 运算符返回单个布尔值的相反值。

[布尔运算符]: /documentation/operators/boolean

{% codeExample 'boolean-operators', false %}
  @debug true and true; // true
  @debug true and false; // false

  @debug true or false; // true
  @debug false or false; // false

  @debug not true; // false
  @debug not false; // true
  ===
  @debug true and true  // true
  @debug true and false  // false

  @debug true or false  // true
  @debug false or false  // false

  @debug not true  // false
  @debug not false  // true
{% endcodeExample %}

## 使用布尔值

你可以使用布尔值来决定是否执行 Sass 中的各种操作。[`@if` 规则][] 在其参数为 `true` 时评估样式块：

[`@if` 规则]: /documentation/at-rules/control/if

{% render 'code_snippets/example-if' %}

[`if()` 函数] 在其参数为 `true` 时返回一个值，在其参数为 `false` 时返回另一个值：

[`if()` 函数]: /documentation/syntax/special-functions#if

{% codeExample 'if-function', false %}
  @debug if(true: 10px; else: 30px); // 10px
  @debug if(false: 10px; else: 30px); // 30px
  ===
  @debug if(true: 10px; else: 30px)  // 10px
  @debug if(false: 10px; else: 30px)  // 30px
{% endcodeExample %}

{% render 'doc_snippets/truthiness-and-falsiness' %}