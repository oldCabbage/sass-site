---
title: "@while"
introduction: >
  `@while` 规则，写成 `@while <expression> { ... }`，如果其 [表达式](/documentation/syntax/structure#expressions) 返回 `true`，则评估其代码块。
  然后，如果其表达式仍然返回 `true`，则再次评估其代码块。此过程会一直持续到表达式最终返回 `false`。
---

{% codeExample 'while' %}
  @use "sass:math";

  /// 将 `$value` 除以 `$ratio`，直到其小于 `$base`。
  @function scale-below($value, $base, $ratio: 1.618) {
    @while $value > $base {
      $value: math.div($value, $ratio);
    }
    @return $value;
  }

  $normal-font-size: 16px;
  sup {
    font-size: scale-below(20px, 16px);
  }
  ===
  @use "sass:math"

  /// 将 `$value` 除以 `$ratio`，直到其小于 `$base`。
  @function scale-below($value, $base, $ratio: 1.618)
    @while $value > $base
      $value: math.div($value, $ratio)
    @return $value



  $normal-font-size: 16px
  sup
    font-size: scale-below(20px, 16px)
{% endcodeExample %}

{% headsUp %}
  虽然 `@while` 对于一些特别复杂的样式表是必要的，但通常最好使用 [`@each`][] 或 [`@for`][] 中的一个（如果适用的话）。它们对读者来说更清晰，并且通常编译速度也更快。

  [`@each`]: /documentation/at-rules/control/each
  [`@for`]: /documentation/at-rules/control/for
{% endheadsUp %}

{% render 'doc_snippets/truthiness-and-falsiness' %}