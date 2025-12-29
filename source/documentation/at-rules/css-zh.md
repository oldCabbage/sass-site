---
title: CSS At-Rules
table_of_contents: true
---

{% compatibility 'dart: "1.15.0"', 'libsass: false', 'ruby: false', 'feature: "Name Interpolation"' %}
  LibSass, Ruby Sass, 和旧版本的 Dart Sass 不支持
  [interpolation][] 在 at-rule 名称中。它们支持在值中使用插值。

  [interpolation]: /documentation/interpolation
{% endcompatibility %}

Sass 支持 CSS 正式规范中的所有 at-rules。为了保持灵活性并兼容 CSS 的未来版本，Sass 默认提供了对几乎所有 at-rules 的通用支持。CSS at-rule 的写法是 `@<name>
<value>`，`@<name> { ... }`，或 `@<name> <value> { ... }`。名称必须是一个标识符，值（如果存在）可以是几乎任何内容。名称和值都可以包含 [interpolation][]。

[interpolation]: /documentation/interpolation

{% codeExample 'css' %}
  @namespace svg url(http://www.w3.org/2000/svg);

  @font-face {
    font-family: "Open Sans";
    src: url("/fonts/OpenSans-Regular-webfont.woff2") format("woff2");
  }

  @counter-style thumbs {
    system: cyclic;
    symbols: "\1F44D";
  }
  ===
  @namespace svg url(http://www.w3.org/2000/svg)

  @font-face
    font-family: "Open Sans"
    src: url("/fonts/OpenSans-Regular-webfont.woff2") format("woff2")

  @counter-style thumbs
    system: cyclic
    symbols: "\1F44D"
{% endcodeExample %}

如果一个 CSS at-rule 嵌套在一个样式规则中，它们会自动交换位置，使得 at-rule 位于 CSS 输出的顶层，而样式规则在其内部。这使得添加条件样式变得容易，而无需重写样式规则的选择器。

{% codeExample 'nested-css-at-rule' %}
  .print-only {
    display: none;

    @media print { display: block; }
  }
  ===
  .print-only
    display: none

    @media print
      display: block
{% endcodeExample %}

## `@media`

{% compatibility 'dart: "1.11.0"', 'libsass: false', 'ruby: "3.7.0"', 'feature: "Range Syntax"' %}
  LibSass 和旧版本的 Dart Sass 和 Ruby Sass 不支持使用 [range context][] 编写的特性媒体查询。它们支持其他标准媒体查询。

  [range context]: https://www.w3.org/TR/mediaqueries-4/#mq-range-context

  {% codeExample 'range-syntax' %}
    @media (width <= 700px) {
      body {
        background: green;
      }
    }
    ===
    @media (width <= 700px)
      body
        background: green
  {% endcodeExample %}
{% endcompatibility %}

[`@media` rule][] 除了允许插值，还允许在 [feature queries][] 中直接使用 [SassScript expressions][]。

[`@media` rule]: https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries
[SassScript expressions]: /documentation/syntax/structure#expressions
[feature queries]: https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries#Targeting_media_features

{% codeExample 'media-rule' %}
  $layout-breakpoint-small: 960px;

  @media (min-width: $layout-breakpoint-small) {
    .hide-extra-small {
      display: none;
    }
  }
  ===
  $layout-breakpoint-small: 960px

  @media (min-width: $layout-breakpoint-small)
    .hide-extra-small
      display: none
{% endcodeExample %}

如果可能，Sass 还会合并嵌套的媒体查询，以方便支持尚不原生支持嵌套 `@media` 规则的浏览器。

{% codeExample 'merge-media-queries' %}
  @media (hover: hover) {
    .button:hover {
      border: 2px solid black;

      @media (color) {
        border-color: #036;
      }
    }
  }
  ===
  @media (hover: hover)
    .button:hover
      border: 2px solid black

      @media (color)
        border-color: #036
{% endcodeExample %}

## `@supports`

[`@supports` rule][] 也允许在声明查询中使用 [SassScript expressions][]。

[SassScript expressions]: /documentation/syntax/structure#expressions
[`@supports` rule]: https://developer.mozilla.org/en-US/docs/Web/CSS/@supports

{% codeExample 'support-at-rule' %}
  @mixin sticky-position {
    position: fixed;
    @supports (position: sticky) {
      position: sticky;
    }
  }

  .banner {
    @include sticky-position;
  }
  ===
  @mixin sticky-position
    position: fixed
    @supports (position: sticky)
      position: sticky



  .banner
    @include sticky-position
{% endcodeExample %}

## `@keyframes`

[`@keyframes` rule][] 与通用 at-rule 类似，不同之处在于其子规则必须是有效的关键帧规则（`<number>%`，`from` 或 `to`），而不是普通选择器。

[`@keyframes` rule]: https://developer.mozilla.org/en-US/docs/Web/CSS/@keyframes

{% codeExample 'keyframes' %}
  @keyframes slide-in {
    from {
      margin-left: 100%;
      width: 300%;
    }

    70% {
      margin-left: 90%;
      width: 150%;
    }

    to {
      margin-left: 0%;
      width: 100%;
    }
  }
  ===
  @keyframes slide-in
    from
      margin-left: 100%
      width: 300%


    70%
      margin-left: 90%
      width: 150%


    to
      margin-left: 0%
      width: 100%
{% endcodeExample %}
