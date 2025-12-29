---
title: 运算符
introduction: >
  Sass 支持一些用于处理不同值的有用 `运算符`。这些包括标准的数学运算符，如 `+` 和 `*`，以及用于各种其他类型的运算符：
---

{% render 'doc_snippets/operator-list', parens: false %}

{% headsUp %}
  Sass 早期的历史中，它添加了对颜色进行数学运算的支持。这些运算对每个颜色的 RGB 通道分别进行操作，因此将两个颜色相加会生成一个其红色通道为它们红色通道之和的颜色，依此类推。

  [colors]: /documentation/values/colors

  这种行为并不太有用，因为逐通道的 RGB 算术运算与人类感知颜色的方式并不对应。[颜色函数][]被添加，它们要实用得多，颜色运算被弃用。它们在 LibSass 和 Ruby Sass 中仍然被支持，但会产生警告，并强烈建议用户避免使用它们。

  [Color functions]: /documentation/modules/color
{% endheadsUp %}

## 运算顺序

Sass 的运算顺序相当标准，从最紧密到最宽松：

[order of operations]: https://en.wikipedia.org/wiki/Order_of_operations#Programming_languages

1. 一元运算符 [`not`][], [`+`, `-`][], 和 [`/`][]。
2. [`*`, `/`, 和 `%` 运算符][]。
3. [`+` 和 `-` 运算符][]。
4. [`>`, `>=`, `<` 和 `<=` 运算符][]。
5. [`==` 和 `!=` 运算符][]。
6. [`and` 运算符][]。
7. [`or` 运算符][]。
8. [`=` 运算符][], 当它可用时。

[`not`]: /documentation/operators/boolean
[`+`, `-`]: /documentation/operators/numeric#unary-operators
[`/`]: /documentation/operators/string#unary-operators
[`*`, `/`, 和 `%` 运算符]: /documentation/operators/numeric
[`+` 和 `-` 运算符]: /documentation/operators/numeric
[`>`, `>=`, `<` 和 `<=` 运算符]: /documentation/operators/relational
[`==` 和 `!=` 运算符]: /documentation/operators/equality
[`and` 运算符]: /documentation/operators/boolean
[`or` 运算符]: /documentation/operators/boolean
[`=` 运算符]: #single-equals

{% codeExample 'operators', false %}
  @debug 1 + 2 * 3 == 1 + (2 * 3); // true
  @debug true or false and false == true or (false and false); // true
  ===
  @debug 1 + 2 * 3 == 1 + (2 * 3)  // true
  @debug true or false and false == true or (false and false)  // true
{% endcodeExample %}

### 括号

你可以使用括号显式控制运算顺序。括号内的运算总是先于括号外的运算进行。括号甚至可以嵌套，在这种情况下，最内层的括号将首先被计算。

{% codeExample 'parentheses', false %}
  @debug (1 + 2) * 3; // 9
  @debug ((1 + 2) * 3 + 4) * 5; // 65
  ===
  @debug (1 + 2) * 3  // 9
  @debug ((1 + 2) * 3 + 4) * 5  // 65
{% endcodeExample %}

## 单等号

Sass 支持一个特殊的 `=` 运算符，它仅允许在函数参数中使用，该运算符只是创建一个以 `=` 分隔其两个操作数的 [非引号字符串][]。这仅用于向后兼容非常旧的仅限 IE 的语法。

[unquoted string]: /documentation/values/strings#unquoted

{% codeExample 'single-equals' %}
  .transparent-blue {
    filter: chroma(color=#0000ff);
  }
  ===
  .transparent-blue
    filter: chroma(color=#0000ff)
{% endcodeExample %}