---
title: "@for"
introduction: >
  `@for` 规则，写法为 `@for <variable> from <expression> to <expression> {
  ... }` 或 `@for <variable> from <expression> through <expression> { ... }`，
  从一个数字（第一个 [expression](/documentation/syntax/structure#expressions) 的结果）计数到另一个数字（第二个的
  结果），并对中间的每个数字计算一个代码块。沿途的每个数字都会被赋值给给定的变量名。如果使用 `to`，最终的数字将被排除；如果使用 `through`，则会被包含。
---

{% codeExample 'for' %}
  $base-color: #036;

  @for $i from 1 through 3 {
    ul:nth-child(3n + #{$i}) {
      background-color: lighten($base-color, $i * 5%);
    }
  }
  ===
  $base-color: #036

  @for $i from 1 through 3
    ul:nth-child(3n + #{$i})
      background-color: lighten($base-color, $i * 5%)
{% endcodeExample %}