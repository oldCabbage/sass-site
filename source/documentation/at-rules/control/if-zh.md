---
title: "@if 和 @else"
table_of_contents: true
introduction: >
  `@if` 规则写成 `@if <expression> { ... }`，用于控制其代码块是否被评估（包括输出任何样式为 CSS）。[表达式](/documentation/syntax/structure#expressions)通常返回 [`true` 或 `false`](/documentation/values/booleans)—如果表达式返回 `true`，则代码块会被评估；如果返回 `false`，则不会。
---

{% render 'code_snippets/example-if' %}

## `@else`

一个 `@if` 规则后面可以可选地跟一个 `@else` 规则，写成 `@else { ... }`。如果 `@if` 表达式返回 `false`，则这个规则的代码块会被评估。

{% codeExample 'if' %}
  $light-background: #f2ece4;
  $light-text: #036;
  $dark-background: #6b717f;
  $dark-text: #d2e1dd;

  @mixin theme-colors($light-theme: true) {
    @if $light-theme {
      background-color: $light-background;
      color: $light-text;
    } @else {
      background-color: $dark-background;
      color: $dark-text;
    }
  }

  .banner {
    @include theme-colors($light-theme: true);
    body.dark & {
      @include theme-colors($light-theme: false);
    }
  }
  ===
  $light-background: #f2ece4
  $light-text: #036
  $dark-background: #6b717f
  $dark-text: #d2e1dd

  @mixin theme-colors($light-theme: true)
    @if $light-theme
      background-color: $light-background
      color: $light-text
    @else
      background-color: $dark-background
      color: $dark-text



  .banner
    @include theme-colors($light-theme: true)
    body.dark &
      @include theme-colors($light-theme: false)
{% endcodeExample %}

条件表达式可以包含 [布尔运算符][] (`and`, `or`, `not`)。

[布尔运算符]: /documentation/operators/boolean

### `@else if`

您也可以通过写 `@else if <expression> { ... }` 来选择是否评估一个 `@else` 规则的代码块。如果这样做，只有当前面的 `@if` 的表达式返回 `false` *且* `@else if` 的表达式返回 `true` 时，该代码块才会被评估。

实际上，您可以在 `@if` 后面链式地写任意数量的 `@else if`。链中第一个表达式返回 `true` 的代码块会被评估，其余的不会。如果链的末尾有一个普通的 `@else`，则在其所有其他代码块失败时，其代码块会被评估。

{% codeExample 'else' %}
  @use "sass:math";

  @mixin triangle($size, $color, $direction) {
    height: 0;
    width: 0;

    border-color: transparent;
    border-style: solid;
    border-width: math.div($size, 2);

    @if $direction == up {
      border-bottom-color: $color;
    } @else if $direction == right {
      border-left-color: $color;
    } @else if $direction == down {
      border-top-color: $color;
    } @else if $direction == left {
      border-right-color: $color;
    } @else {
      @error "Unknown direction #{$direction}.";
    }
  }

  .next {
    @include triangle(5px, black, right);
  }
  ===
  @use "sass:math"

  @mixin triangle($size, $color, $direction)
    height: 0
    width: 0

    border-color: transparent
    border-style: solid
    border-width: math.div($size, 2)

    @if $direction == up
      border-bottom-color: $color
    @else if $direction == right
      border-left-color: $color
    @else if $direction == down
      border-top-color: $color
    @else if $direction == left
      border-right-color: $color
    @else
      @error "Unknown direction #{$direction}."



  .next
    @include triangle(5px, black, right)
{% endcodeExample %}

{% render 'doc_snippets/truthiness-and-falsiness' %}