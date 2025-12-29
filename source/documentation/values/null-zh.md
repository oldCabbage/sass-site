---
title: "null"
introduction: >
  值 `null` 是其类型的唯一值。它表示值的缺失，并且通常由 [functions](/documentation/at-rules/function) 返回以指示结果的缺失。
---

{% codeExample 'null', false %}
  @use "sass:map";
  @use "sass:string";

  @debug string.index("Helvetica Neue", "Roboto"); // null
  @debug map.get(("large": 20px), "small"); // null
  @debug &; // null
  ===
  @use "sass:map"
  @use "sass:string"

  @debug string.index("Helvetica Neue", "Roboto")  // null
  @debug map.get(("large": 20px), "small")  // null
  @debug &  // null
{% endcodeExample %}

如果一个 [list][] 包含 `null`，该 `null` 将从生成的 CSS 中省略。

[list]: /documentation/values/lists

{% codeExample 'null-lists' %}
  $fonts: ("serif": "Helvetica Neue", "monospace": "Consolas");

  h3 {
    font: 18px bold map-get($fonts, "sans");
  }
  ===
  $fonts: ("serif": "Helvetica Neue", "monospace": "Consolas")

  h3
    font: 18px bold map-get($fonts, "sans")
{% endcodeExample %}

如果属性值为 `null`，该属性将完全省略。

{% codeExample 'null-value-omitted' %}
  $fonts: ("serif": "Helvetica Neue", "monospace": "Consolas");

  h3 {
    font: {
      size: 18px;
      weight: bold;
      family: map-get($fonts, "sans");
    }
  }
  ===
  $fonts: ("serif": "Helvetica Neue", "monospace": "Consolas")

  h3
    font:
      size: 18px
      weight: bold
      family: map-get($fonts, "sans")
{% endcodeExample %}

`null` 也是 [*falsey*][]，这意味着对于任何接受布尔值的规则或 [operators][]，它被视为 `false`。这使得可以轻松地将可能为 `null` 的值用作 [`@if`][] 和 [`if()`] 的条件。

[*falsey*]: /documentation/at-rules/control/if#truthiness-and-falsiness
[operators]: /documentation/operators/boolean
[`@if`]: /documentation/at-rules/control/if
[`if()`]: /documentation/syntax/special-functions#if

{% render 'code_snippets/example-if-parent-selector' %}