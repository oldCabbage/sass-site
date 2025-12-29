---
title: "@each"
introduction: >
  `@each` 规则使得为 [列表](/documentation/values/lists) 的每个元素或 [映射](/documentation/values/maps) 的每对键值对生成样式或评估代码变得容易。它非常适合那些只有少量变化的重复样式。通常写成 `@each <variable> in <expression> { ... }`，其中
  [expression](/documentation/syntax/structure#expressions) 返回一个列表。块会依次为列表中的每个元素评估，并将其分配给给定的变量名。
---

{% render 'code_snippets/example-each-list' %}

## 与映射一起使用

你也可以通过写 `@each <variable>, <variable> in <expression> { ... }` 来使用 `@each` 遍历映射中的每个键/值对。键会被分配给第一个变量名，值会被分配给第二个。

{% render 'code_snippets/example-each-map' %}

## 解构

如果你有一个列表的列表，你可以通过写 `@each <variable...> in <expression> { ... }` 来使用 `@each` 自动将变量分配给内部列表中的每个值。这称为*解构*，因为变量与内部列表的结构匹配。每个变量名会被分配给列表中相应位置的值，如果列表中没有足够的值，则分配为 [`null`][]。

[`null`]: /documentation/values/null

{% codeExample 'each' %}
  $icons:
    "eye" "\f112" 12px,
    "start" "\f12e" 16px,
    "stop" "\f12f" 10px;

  @each $name, $glyph, $size in $icons {
    .icon-#{$name}:before {
      display: inline-block;
      font-family: "Icon Font";
      content: $glyph;
      font-size: $size;
    }
  }
  ===
  $icons: "eye" "\f112" 12px, "start" "\f12e" 16px, "stop" "\f12f" 10px




  @each $name, $glyph, $size in $icons
    .icon-#{$name}:before
      display: inline-block
      font-family: "Icon Font"
      content: $glyph
      font-size: $size
{% endcodeExample %}

{% funFact %}
  因为 `@each` 支持解构并且 [映射被视为列表的列表][]，`@each` 的映射支持无需特别为映射提供支持即可工作。

  [maps count as lists of lists]: /documentation/values/maps
{% endfunFact %}
