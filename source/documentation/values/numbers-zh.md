---
title: 数字
introduction: >
  Sass 中的数字有两个组成部分：数字本身及其单位。例如，在 `16px` 中，数字是 `16`，单位是 `px`。数字可以没有单位，也可以有复杂的单位。有关更多详细信息，请参见下方的 [单位](#units)。
---

{% codeExample 'numbers', false %}
  @debug 100; // 100
  @debug 0.8; // 0.8
  @debug 16px; // 16px
  @debug 5px * 2px; // 10px*px (读作 "平方像素")
  ===
  @debug 100  // 100
  @debug 0.8  // 0.8
  @debug 16px  // 16px
  @debug 5px * 2px  // 10px*px (读作 "平方像素")
{% endcodeExample %}

Sass 数字支持与 CSS 数字相同的格式，包括 [科学计数法][]，科学计数法通过在数字和其 10 的幂之间使用 `e` 来表示。由于浏览器对科学计数法的支持历史上一直很不稳定，Sass 总是将其编译为完全展开的数字。

[科学计数法]: https://en.wikipedia.org/wiki/Scientific_notation

{% codeExample 'scientific-notation', false %}
  @debug 5.2e3; // 5200
  @debug 6e-2; // 0.06
  ===
  @debug 5.2e3  // 5200
  @debug 6e-2  // 0.06
{% endcodeExample %}

{% headsUp %}
  Sass 不区分整数和小数，因此例如 `math.div(5, 2)` 返回 `2.5` 而不是 `2`。这与 JavaScript 的行为相同，但与其他许多编程语言不同。
{% endheadsUp %}

{% render 'doc_snippets/number-units' %}

## 精度

{% compatibility 'dart: true', 'libsass: false', 'ruby: "3.5.0"', 'feature: "10 Digit Default"' %}
  LibSass 和较早版本的 Ruby Sass 默认使用 5 位数值精度，但可以配置为使用不同的数字。建议用户将其配置为 10 位，以获得更高的精度和向前兼容性。
{% endcompatibility %}

Sass 数字在内部表示为 64 位浮点值。当序列化为 CSS 和用于相等性比较时，它们支持最多小数点后 10 位精度。这意味着几个不同的事情：

* 仅在生成的 CSS 中包含数字小数点后前 10 位。

* 像 [`==`][] 和 [`>=`][] 这样的操作符会认为两个数字在小数点后第 10 位之前相同则是等价的。

* 如果一个数字小于 `0.0000000001` 远离一个整数，则对于像 [`list.nth()`][] 这样的需要整数参数的函数，它被视为整数。

[`==`]: /documentation/operators/equality
[`>=`]: /documentation/operators/relational
[`list.nth()`]: /documentation/modules/list#nth

{% codeExample 'precision', false %}
  @debug 0.012345678912345; // 0.0123456789
  @debug 0.01234567891 == 0.01234567899; // true
  @debug 1.00000000009; // 1
  @debug 0.99999999991; // 1
  ===
  @debug 0.012345678912345  // 0.0123456789
  @debug 0.01234567891 == 0.01234567899  // true
  @debug 1.00000000009  // 1
  @debug 0.99999999991  // 1
{% endcodeExample %}

{% funFact %}
  数字在需要精度的地方是以 *惰性* 方式舍入到 10 位精度的。这意味着数学函数会在内部使用完整数字值以避免累积额外的舍入误差。
{% endfunFact %}