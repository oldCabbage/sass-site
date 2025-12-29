---
title: "@function"
table_of_contents: true
introduction: >
  函数允许你在样式表中定义复杂的 [SassScript 值](/documentation/values) 操作，并且可以在整个样式表中重复使用。它们使得以可读的方式抽象出常见的公式和行为变得容易。
---

函数使用 `@function` 规则定义，写法为 `@function <name>(<arguments...>) { ... }`。函数的名称可以是任何不以 `--` 开头的 Sass 标识符。它只能包含 [通用语句]，以及 [`@return` 规则]，该规则指示函数调用的结果值。函数使用正常的 CSS 函数语法调用。

[通用语句]: /documentation/syntax/structure#universal-statements
[`@return` 规则]: #return

{% codeExample 'functions' %}
  @function fibonacci($n) {
    $sequence: 0 1;
    @for $_ from 1 through $n {
      $new: nth($sequence, length($sequence)) + nth($sequence, length($sequence) - 1);
      $sequence: append($sequence, $new);
    }
    @return nth($sequence, length($sequence));
  }

  .sidebar {
    float: left;
    margin-left: fibonacci(4) * 1px;
  }
  ===
  @function fibonacci($n)
    $sequence: 0 1
    @for $_ from 1 through $n
      $new: nth($sequence, length($sequence)) + nth($sequence, length($sequence) - 1)
      $sequence: append($sequence, $new)
    @return nth($sequence, length($sequence))

  .sidebar
    float: left
    margin-left: fibonacci(4) * 1px
{% endcodeExample %}

{% funFact %}
  函数名称，像所有 Sass 标识符一样，将连字符和下划线视为相同。这意味着 `scale-color` 和 `scale_color` 都指同一个函数。这是 Sass 最初只允许标识符名称中使用下划线的历史遗留问题。一旦 Sass 增加了对连字符的支持以匹配 CSS 的语法，这两种写法就被视为等价的，以简化迁移。
{% endfunFact %}

{% headsUp %}
  虽然技术上函数可以有副作用，比如设置 [全局变量][]，但这强烈不推荐。使用 [混合宏][] 来处理副作用，使用函数仅用于计算值。

  [全局变量]: /documentation/variables#scope
  [混合宏]: /documentation/at-rules/mixin
{% endheadsUp %}

## 参数

{% comment %}
  在更改此部分时，请记得同时更改混合宏参数部分！
{% endcomment %}

参数允许在每次调用函数时自定义函数的行为。参数在 `@function` 规则中函数名称后面指定，以括号括起来的变量名称列表形式。函数必须使用与参数数量相同的 [SassScript 表达式][] 形式进行调用。这些表达式的值在函数体内作为相应的变量可用。

[SassScript 表达式]: /documentation/syntax/structure#expressions

{% funFact %}
  参数列表也可以有尾随逗号！这使得在重构样式表时更容易避免语法错误。
{% endfunFact %}

### 可选参数

通常，函数声明的每个参数在包含该函数时都必须传递。然而，你可以通过定义一个 *默认值* 来使参数可选，如果未传递该参数，则使用该默认值。默认值使用与 [变量声明][] 相同的语法：变量名称，后跟冒号和 [SassScript 表达式][]。这使得定义灵活的函数 API 变得容易，这些 API 可以以简单或复杂的方式使用。

[变量声明]: /documentation/variables
[SassScript 表达式]: /documentation/syntax/structure#expressions

{% codeExample 'optional-arguments' %}
  @function invert($color, $amount: 100%) {
    $inverse: change-color($color, $hue: hue($color) + 180);
    @return mix($inverse, $color, $amount);
  }

  $primary-color: #036;
  .header {
    background-color: invert($primary-color, 80%);
  }
  ===
  @function invert($color, $amount: 100%)
    $inverse: change-color($color, $hue: hue($color) + 180)
    @return mix($inverse, $color, $amount)


  $primary-color: #036
  .header
    background-color: invert($primary-color, 80%)
{% endcodeExample %}

{% funFact %}
  默认值可以是任何 SassScript 表达式，甚至可以引用前面的参数！
{% endfunFact %}

### 关键字参数

在调用函数时，除了按参数列表中的位置传递参数外，还可以按名称传递参数。这对于具有多个可选参数的函数，或具有 [布尔值][] 参数的函数特别有用，这些参数没有名称时其含义不明显。关键字参数使用与 [变量声明][] 和 [可选参数][] 相同的语法。

[变量声明]: /documentation/variables
[布尔值]: /documentation/values/booleans
[可选参数]: #optional-arguments

{% codeExample 'keyword-arguments' %}
  $primary-color: #036;
  .banner {
    background-color: $primary-color;
    color: scale-color($primary-color, $lightness: +40%);
  }
  ===
  $primary-color: #036
  .banner
    background-color: $primary-color
    color: scale-color($primary-color, $lightness: +40%)
{% endcodeExample %}

{% headsUp %}
  因为 *任何* 参数都可以按名称传递，所以在重命名函数的参数时要小心……可能会破坏用户的代码！可以将旧名称作为 [可选参数][] 保留一段时间，并在有人传递它时打印 [警告][]，以便他们知道要迁移到新参数。

  [可选参数]: #optional-arguments
  [警告]: /documentation/at-rules/warn
{% endheadsUp %}

### 接受任意参数

有时让函数能够接受任意数量的参数是有用的。如果 `@function` 声明中的最后一个参数以 `...` 结尾，则传递给该函数的所有额外参数都会作为 [列表][] 传递给该参数。这个参数被称为 [参数列表][]。

[列表]: /documentation/values/lists
[参数列表]: /documentation/values/lists#argument-lists

{% codeExample 'taking-arbitrary-arguments' %}
  @function sum($numbers...) {
    $sum: 0;
    @each $number in $numbers {
      $sum: $sum + $number;
    }
    @return $sum;
  }

  .micro {
    width: sum(50px, 30px, 100px);
  }
  ===
  @function sum($numbers...)
    $sum: 0
    @each $number in $numbers
      $sum: $sum + $number

    @return $sum


  .micro
    width: sum(50px, 30px, 100px)
{% endcodeExample %}

#### 接受任意关键字参数

参数列表也可以用于接受任意关键字参数。[`meta.keywords()` 函数][] 接受一个参数列表，并将传递给函数的任何额外关键字作为参数名称（不包括 `$`）到这些参数值的 [映射][] 返回。

[`meta.keywords()` 函数]: /documentation/modules/meta#keywords
[映射]: /documentation/values/maps

{% funFact %}
  如果你从未将参数列表传递给 [`meta.keywords()` 函数][]，该参数列表将不允许额外的关键字参数。这有助于调用函数的人确保他们没有意外拼写错误的参数名称。

  [`meta.keywords()` 函数]: /documentation/modules/meta#keywords
{% endfunFact %}

#### 传递任意参数

就像参数列表允许函数接受任意位置或关键字参数一样，相同的语法可以用于 *传递* 位置或关键字参数给函数。如果你在函数调用的最后一个参数传递一个列表后跟 `...`，其元素将被视为附加的位置参数。类似地，一个映射后跟 `...` 将被视为附加的关键字参数。你甚至可以同时传递两者！

{% codeExample 'passing-arbitrary-arguments' %}
  $widths: 50px, 30px, 100px;
  .micro {
    width: min($widths...);
  }
  ===
  $widths: 50px, 30px, 100px
  .micro
    width: min($widths...)
{% endcodeExample %}

{% funFact %}
  因为 [参数列表][] 同时跟踪位置参数和关键字参数，因此可以将其同时传递给另一个函数。这使得定义函数别名变得超级容易！

  [参数列表]: /documentation/values/lists#argument-lists

  {% codeExample 'passing-arbitrary-arguments-fun-fact' %}
    @function fg($args...) {
      @warn "The fg() function is deprecated. Call foreground() instead.";
      @return foreground($args...);
    }
    ===
    @function fg($args...)
      @warn "The fg() function is deprecated. Call foreground() instead."
      @return foreground($args...)
  {% endcodeExample %}
{% endfunFact %}

## `@return`

`@return` 规则指示调用函数时要使用的值。它仅允许在 `@function` 体内使用，并且每个 `@function` 必须以 `@return` 结束。

当遇到 `@return` 时，它会立即结束函数并返回其结果。提前返回对于处理边缘情况或在不需要将整个函数包装在 [`@else 块`] 中的情况下使用更高效的算法非常有用。

[`@else 块`]: /documentation/at-rules/control/if#else

{% codeExample 'return', false %}
  @use "sass:string";

  @function str-insert($string, $insert, $index) {
    // 如果不需要，避免创建新字符串。
    @if string.length($string) == 0 {
      @return $insert;
    }

    $before: string.slice($string, 0, $index);
    $after: string.slice($string, $index);
    @return $before + $insert + $after;
  }
  ===
  @use "sass:string"

  @function str-insert($string, $insert, $index)
    // 如果不需要，避免创建新字符串。
    @if string.length($string) == 0
      @return $insert


    $before: string.slice($string, 0, $index)
    $after: string.slice($string, $index)
    @return $before + $insert + $after
{% endcodeExample %}

## 其他函数

除了用户定义的函数外，Sass 还提供了丰富的 [核心库][] 内置函数，这些函数始终可用。Sass 实现还允许在宿主语言中定义 [自定义函数][]。当然，你也可以调用 [纯 CSS 函数][]（甚至包括 [奇怪的语法][]）。

[核心库]: /documentation/modules
[自定义函数]: /documentation/js-api/interfaces/LegacySharedOptions#functions
[纯 CSS 函数]: #plain-css-functions
[奇怪的语法]: /documentation/syntax/special-functions

### 纯 CSS 函数

任何既不是用户定义的也不是 [内置](/documentation/modules) 函数的函数调用都会被编译为纯 CSS 函数（除非它使用了 [Sass 参数语法](/documentation/at-rules/function/#arguments)）。参数将被编译为 CSS 并作为函数调用的一部分包含在内。这确保了 Sass 支持所有 CSS 函数，而无需每次添加新函数时发布新版本。

{% codeExample 'plain-css-functions', false %}
  @debug var(--main-bg-color); // var(--main-bg-color)

  $primary: #f2ece4;
  $accent: #e1d7d2;
  @debug radial-gradient($primary, $accent); // radial-gradient(#f2ece4, #e1d7d2)
  ===
  @debug var(--main-bg-color)  // var(--main-bg-color)

  $primary: #f2ece4
  $accent: #e1d7d2
  @debug radial-gradient($primary, $accent)  // radial-gradient(#f2ece4, #e1d7d2)
{% endcodeExample %}

{% headsUp %}
  因为任何未知函数都会被编译为 CSS，所以很容易错过拼写错误的函数名称。考虑在样式表输出上运行 [CSS 检查工具][] 以在发生这种情况时收到通知！

  [CSS 检查工具]: https://stylelint.io/
{% endheadsUp %}

{% funFact %}
  一些 CSS 函数，如 `calc()` 和 `element()` 有不寻常的语法。Sass [特殊解析这些函数][] 作为 [非引号字符串][]。

  [特殊解析这些函数]: /documentation/syntax/special-functions
  [非引号字符串]: /documentation/values/strings#unquoted
{% endfunFact %}