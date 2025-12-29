---
title: 计算
introduction: >
  计算是 Sass 表示 `calc()` 函数的方式，以及类似的函数如 `clamp()`、`min()` 和 `max()`。Sass 将尽可能简化这些函数，即使它们相互组合也是如此。
---

{% compatibility 'dart: "1.40.0"', 'libsass: false', 'ruby: false' %}
  LibSass、Ruby Sass 和 1.40.0 之前的 Dart Sass 版本将 `calc()` 解析为类似于 `element()` 的 [特殊函数]。

  [特殊函数]: /documentation/syntax/special-functions#element-progid-and-expression

  LibSass、Ruby Sass 和 1.31.0 之前的 Dart Sass 版本将 `clamp()` 解析为 [普通 CSS 函数]，而不是支持其内部的特殊语法。1.31.0 到 1.40.0 之间的 Dart Sass 版本将 `clamp()` 解析为类似于 `element()` 的 [特殊函数]。

  [普通 CSS 函数]: /documentation/at-rules/function/#plain-css-functions
{% endcompatibility %}

{% compatibility 'dart: "1.67.0"', 'libsass: false', 'ruby: false', 'feature: "Adjacent values"' %}
  1.40.0 到 1.67.0 之间的 Dart Sass 版本不允许在不使用运算符分隔的情况下在计算中使用多个值，即使像 `calc(1 var(--plus-two))` 这样的情况是有效的 CSS（因为 `--plus-two` 可以被定义为 `+ 2`）。

  从 Dart Sass 1.67.0 开始，计算中的多个值可以用空格分隔，只要每个其他值求值为非引号字符串（例如 `var()` 表达式或非引号字符串 `"+ 2"`）。
{% endcompatibility %}

{% codeExample 'calculations', false %}
  @debug calc(400px + 10%); // calc(400px + 10%)
  @debug calc(400px / 2); // 200px
  @debug min(100px, calc(1rem + 10%)); // min(100px, 1rem + 10%)
  ===
  @debug calc(400px + 10%)  // calc(400px + 10%)
  @debug calc(400px / 2)  // 200px
  @debug min(100px, calc(1rem + 10%)) ; // min(100px, 1rem + 10%)
{% endcodeExample %}

计算使用一种与普通 SassScript 不同的特殊语法。它与 CSS 的 `calc()` 语法相同，但增加了使用 [Sass 变量] 和调用 [Sass 函数] 的能力。这意味着在计算中 `/` 始终是除法运算符！

[Sass 变量]: /documentation/variables
[Sass 函数]: /documentation/modules

{% funFact %}
  Sass 函数调用的参数使用普通的 Sass 语法，而不是特殊的计算语法！
{% endfunFact %}

你也可以在计算中使用 [插值]。但是，如果你这样做，涉及该插值的任何操作都不会被简化或类型检查，因此很容易导致冗长或无效的 CSS。与其写 `calc(10px + #{$var})`，只需写 `calc(10px + $var)`！

[插值]: /documentation/interpolation

## 简化

如果计算中的相邻操作使用可以在编译时组合的单位（例如 `1in + 10px` 或 `5s * 2`），Sass 将简化这些操作。如果可能，它甚至会将整个计算简化为一个单一的数字——例如，`clamp(0px, 30px, 20px)` 将返回 `20px`。

{% headsUp %}
  这意味着计算表达式不一定总是返回一个计算！如果你正在编写一个 Sass 库，你可以始终使用 [`meta.type-of()`] 函数来确定你正在处理的类型。

  [`meta.type-of()`]: /documentation/modules/meta#type-of
{% endheadsUp %}

计算也会在其他计算中被简化。特别是，如果 `calc()` 出现在任何其他计算中，函数调用将被移除，并被替换为一个普通的操作。

{% codeExample 'simplification' %}
  $width: calc(400px + 10%);

  .sidebar {
    width: $width;
    padding-left: calc($width / 4);
  }
  ===
  $width: calc(400px + 10%)

  .sidebar
    width: $width
    padding-left: calc($width / 4)
{% endcodeExample %}

## 操作

你不能将计算与普通的 SassScript 操作（如 `+` 和 `*`）一起使用。如果你想编写一些允许计算的数学函数，只需将它们写在它们自己的 `calc()` 表达式中——如果它们传入了具有兼容单位的一堆数字，它们将返回纯数字，如果它们传入了计算，它们将返回计算。

这种限制是为了确保如果不需要计算，它们会在尽可能早的时候抛出错误。计算不能在任何地方使用纯数字：例如，它们不能被注入到 CSS 标识符（如 `.item-#{$n}`）中，并且不能传递给 Sass 的内置 [数学函数]。保留 SassScript 操作用于纯数字使计算允许和不允许的地方变得非常清楚。

[数学函数]: /documentation/modules/math

{% codeExample 'calc-operations', false %}
  $width: calc(100% + 10px);
  @debug $width * 2; // 错误！
  @debug calc($width * 2); // calc((100% + 10px) * 2);
  ===
  $width: calc(100% + 10px);
  @debug $width * 2; // 错误！
  @debug calc($width * 2); // calc((100% + 10px) * 2);
{% endcodeExample %}

## 常量

{% compatibility 'dart: "1.60.0"','libsass: false', 'ruby: false' %}{% endcompatibility %}

计算也可以包含常量，这些常量写为 CSS 标识符。为了与未来的 CSS 规范兼容，*所有* 标识符都是允许的，默认情况下，它们只是被视为按原样传递的非引号字符串。

{% codeExample 'calc-constants', false %}
  @debug calc(h + 30deg); // calc(h + 30deg);
  ===
  @debug calc(h + 30deg)  // calc(h + 30deg);
{% endcodeExample %}

Sass 自动解析 CSS 规范中指定的几个特殊常量名称为无单位数字：

* `pi` 是数学常量 *π* 的简写。

  [数学常量 *π*]: https://en.wikipedia.org/wiki/Pi

* `e` 是数学常量 *e* 的简写。

  [数学常量 *e*]: https://en.wikipedia.org/wiki/E_(mathematical_constant)

* `infinity`、`-infinity` 和 `NaN` 表示相应的浮点值。

{% codeExample 'unitless-numbers', false %}
  @use 'sass:math';

  @debug calc(pi); // 3.1415926536
  @debug calc(e);  // 2.7182818285
  @debug calc(infinity) > math.$max-number;  // true
  @debug calc(-infinity) < math.$min-number; // true
  ===
  @use 'sass:math'

  @debug calc(pi)  // 3.1415926536
  @debug calc(e)   // 2.7182818285
  @debug calc(infinity) > math.$max-number   // true
  @debug calc(-infinity) < math.$min-number  // true
{% endcodeExample %}

## 计算函数

{% compatibility 'dart: "1.65.0"', 'libsass: false', 'ruby: false', 'feature: "Additional functions"' %}
  Dart Sass 1.65.0 及更高版本（*除* 1.66.x）处理这些计算函数的执行：`round()`、`mod()`、`rem()`、`sin()`、`cos()`、`tan()`、`asin()`、`acos()`、`atan()`、`atan2()`、`pow()`、`sqrt()`、`hypot()`、`log()`、`exp()`、`abs()` 和 `sign()`。

  在 Dart Sass 1.65.x 中，任何函数调用，如果名称与计算函数匹配，则*始终*被解析为计算函数。这破坏了一些现有的用户定义函数，因此在 1.66.0 中移除了对新计算函数的支持，直到在 1.67.0 中可以*不*破坏现有行为的情况下重新添加。
{% endcompatibility %}

Sass 将以下函数解析为 [计算]：
* 比较函数：[`min()`]、[`max()`] 和 [`clamp()`]
* 分步值函数：[`round()`]、[`mod()`] 和 [`rem()`]。
* 三角函数：[`sin()`]、[`cos()`]、[`tan()`]、[`asin()`]、[`acos()`]、[`atan()`] 和 [`atan2()`]。
* 指数函数：[`pow()`]、[`sqrt()`]、[`hypot()`]、[`log()`] 和 [`exp()`]。
* 符号相关函数：[`abs()`] 和 [`sign()`]。

[计算]: https://www.w3.org/TR/css-values-4/#math
[`min()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/min
[`max()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/max
[`clamp()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/clamp
[`round()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/round
[`abs()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/abs
[`sin()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/sin
[`cos()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/cos
[`tan()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/tan
[`asin()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/asin
[`acos()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/acos
[`atan()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/atan
[`atan2()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/atan2
[`pow()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/pow
[`sqrt()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/sqrt
[`hypot()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/hypot
[`log()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/log
[`exp()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/exp
[`mod()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/mod
[`rem()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/rem
[`sign()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/sign

{% funFact %}
  如果你定义了一个与计算函数同名的 [Sass 函数]，Sass 将始终调用你的函数而不是创建计算值。

  [Sass 函数]: /documentation/at-rules/function
{% endfunFact %}

### 旧的全局函数

CSS 在 Values and Units Level 4 中添加了对 [数学表达式] 的支持。然而，Sass 在此之前很久就支持了它自己的 [`round()`]、[`abs()`]、[`min()`] 和 [`max()`]，并且需要与所有这些现有的样式表向后兼容。这导致了对额外特殊的语法聪明才智的需求。

[数学表达式]: https://www.w3.org/TR/css-values-4/#math
[`round()`]: ../modules/math#round
[`abs()`]: ../modules/math#abs
[`min()`]: ../modules/math#min
[`max()`]: ../modules/math#max

如果 `round()`、`abs()`、`min()` 或 `max()` 的调用是一个有效的计算表达式，它将被解析为一个计算。但是一旦调用的任何部分包含一个 SassScript 功能，而该功能在计算中不受支持（如 [取模运算符]），则将其解析为对适当的 Sass 数学函数的调用。

由于计算在可能的情况下会简化为数字，唯一的实质性区别是 Sass 函数只支持可以在构建时组合的单位，因此 `min(12px % 10, 10%)` 将抛出错误。

[取模运算符]: /documentation/operators/numeric/

{% headsUp %}
  其他计算不允许无单位数字被添加到、从或与有单位的数字进行比较。[`min()`]、[`max()`]、[`abs()`] 和 [单参数 `round()`] 是不同的：为了与允许单位/无单位混合的全局 Sass 旧函数向后兼容，这些单位可以混合，只要它们直接包含在 `min()`、`max()`、`abs()` 或单参数 `round()` 计算中。

  例如，`min(5 + 10px, 20px)` 将导致 `15px`。然而 `sqrt(5 + 10px)` 将抛出错误，因为 `sqrt(5 + 10px)` 从未是一个全局 Sass 函数，这些是不兼容的单位。

[单参数 `round()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/round
[`abs()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/abs
[`min()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/min
[`max()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/max
{% endheadsUp %}

#### `min()` 和 `max()`

{% compatibility 'dart: ">=1.11.0 <1.42.0"', 'libsass: false', 'ruby: false', 'feature: "min and max syntax"' %}
  LibSass、Ruby Sass 和 1.11.0 之前的 Dart Sass 版本*始终*将 `min()` 和 `max()` 解析为 Sass 函数。为了在这些实现中创建纯 CSS 的 `min()` 或 `max()` 调用，你可以写类似 `unquote("min(#{$padding}, env(safe-area-inset-left))")` 的内容。

  CSS 在 Values and Units Level 4 中添加了对 [`min()` 和 `max()` 函数] 的支持，随后 Safari [为了支持 iPhoneX] 快速采用了这些函数。由于我们已经支持 `min()` 和 `max()` 作为旧的 Sass 函数，我们不得不实现向后兼容性和作为 CSS 函数的支持。

  1.11.0 到 1.40.0 之间的 Dart Sass 版本，以及 1.40.1 到 1.42.0 之间的 Dart Sass 版本将 `min()` 和 `max()` 函数解析为 [特殊函数]（如果它们是有效的纯 CSS），但如果它们包含除插值之外的 Sass 功能（如变量或函数调用），则解析为 Sass 函数。

  Dart Sass 1.41.0 将 `min()` 和 `max()` 函数解析为计算，但不允许无单位数字与有单位的数字组合。这与全局 `min()` 和 `max()` 函数不兼容，因此该行为被回滚。

  [`min()` 和 `max()` 函数]: https://www.w3.org/TR/css-values-4/#math
  [为了支持 iPhoneX]: https://webkit.org/blog/7929/designing-websites-for-iphone-x/
  [特殊函数]: /documentation/syntax/special-functions/
{% endcompatibility %}

{% codeExample 'min-max' %}
  $padding: 12px;

  .post {
    // 由于这些 max() 调用是有效的计算表达式，它们被解析为计算。
    padding-left: max($padding, env(safe-area-inset-left));
    padding-right: max($padding, env(safe-area-inset-right));
  }

  .sidebar {
    // 由于这些使用了 SassScript 专用的取模运算符，它们被解析为 SassScript 函数调用。
    padding-left: max($padding % 10, 20px);
    padding-right: max($padding % 10, 20px);
  }

  ===
  $padding: 12px

  .post
    // 由于这些 max() 调用是有效的计算表达式，它们被解析为计算。
    padding-left: max($padding, env(safe-area-inset-left))
    padding-right: max($padding, env(safe-area-inset-right))


  .sidebar
    // 由于这些使用了 SassScript 专用的取模运算符，它们被解析为 SassScript 函数调用。
    padding-left: max($padding % 10, 20px)
    padding-right: max($padding % 10, 20px)

  ===
  .post {
    padding-left: max(12px, env(safe-area-inset-left));
    padding-right: max(12px, env(safe-area-inset-right));
  }

  .sidebar {
    padding-left: 20px;
    padding-right: 20px;
  }
{% endcodeExample %}

#### `round()`

{% compatibility 'dart: "1.65.0"', 'libsass: false', 'ruby: false', 'feature: "min and max syntax"' %}
  LibSass、Ruby Sass 和 1.65.0 之前的 Dart Sass 版本，以及 Dart Sass 1.66.x *始终*将 `round()` 解析为 Sass 函数。为了在这些实现中使用纯 CSS 函数，你可以写类似 `round(#{$strategy, $number, $step})` 的内容。
{% endcompat