---
title: 属性声明
table_of_contents: true
introduction: >
  在 Sass 和 CSS 中，属性声明定义了匹配选择器的元素的样式。但是 Sass 添加了一些额外的功能，使它们更易于编写并实现自动化。首先，声明的值可以是任何 [SassScript 表达式](/documentation/syntax/structure#expressions)，这些表达式将被评估并包含在结果中。
---

{% codeExample 'declaration' %}
  .circle {
    $size: 100px;
    width: $size;
    height: $size;
    border-radius: $size * 0.5;
  }
  ===
  .circle
    $size: 100px
    width: $size
    height: $size
    border-radius: $size * 0.5
{% endcodeExample %}

## 插值

属性的名称可以包含 [插值][], 这使得可以根据需要动态生成属性。您甚至可以插值整个属性名称！

[插值]: /documentation/interpolation

{% codeExample 'interpolation' %}
  @mixin prefix($property, $value, $prefixes) {
    @each $prefix in $prefixes {
      -#{$prefix}-#{$property}: $value;
    }
    #{$property}: $value;
  }

  .gray {
    @include prefix(filter, grayscale(50%), moz webkit);
  }
  ===
  @mixin prefix($property, $value, $prefixes)
    @each $prefix in $prefixes
      -#{$prefix}-#{$property}: $value

    #{$property}: $value


  .gray
    @include prefix(filter, grayscale(50%), moz webkit)
{% endcodeExample %}

## 嵌套

许多 CSS 属性以相同的前缀开头，该前缀充当一种命名空间的作用。例如，`font-family`，`font-size` 和 `font-weight` 都以 `font-` 开头。Sass 通过允许属性声明嵌套来简化这一过程。外部属性名称会添加到内部属性名称之前，中间用连字符分隔。

{% codeExample 'nesting' %}
  .enlarge {
    font-size: 14px;
    transition: {
      property: font-size;
      duration: 4s;
      delay: 2s;
    }

    &:hover { font-size: 36px; }
  }
  ===
  .enlarge
    font-size: 14px
    transition:
      property: font-size
      duration: 4s
      delay: 2s

    &:hover
      font-size: 36px
{% endcodeExample %}

其中一些 CSS 属性有简写版本，使用命名空间作为属性名称。对于这些属性，您可以同时编写简写值和更详细的嵌套版本。

{% codeExample 'nesting-shorthand' %}
  .info-page {
    margin: auto {
      bottom: 10px;
      top: 2px;
    }
  }
  ===
  .info-page
    margin: auto
      bottom: 10px
      top: 2px
{% endcodeExample %}

## 隐藏声明

有时您只想让属性声明在某些情况下出现。如果声明的值为 [`null`][] 或空的 [非引号字符串][], Sass 将不会将该声明编译到 CSS 中。

[`null`]: /documentation/values/null
[非引号字符串]: /documentation/values/strings#unquoted

{% codeExample 'hidden-declarations' %}
  $rounded-corners: false;

  .button {
    border: 1px solid black;
    border-radius: if(sass($rounded-corners): 5px);
  }
  ===
  $rounded-corners: false

  .button
    border: 1px solid black
    border-radius: if(sass($rounded-corners): 5px)
{% endcodeExample %}

## 自定义属性

{% compatibility 'dart: true', 'libsass: "3.5.0"', 'ruby: "3.5.0"', 'feature: "SassScript Syntax"' %}
  较旧版本的 LibSass 和 Ruby Sass 解析自定义属性声明的方式与其他属性声明相同，允许使用完整的 SassScript 表达式作为值。即使使用这些版本，建议您使用插值来注入 SassScript 值，以实现向前兼容。

  有关更多详细信息，请参阅 [重大更改页面][]。

  [重大更改页面]: /documentation/breaking-changes/css-vars
{% endcompatibility %}

[CSS 自定义属性][]，也称为 CSS 变量，具有不寻常的声明语法：它们允许在声明值中使用几乎任何文本。此外，这些值可以被 JavaScript 访问，因此任何值都可能对用户相关。这包括通常会被解析为 SassScript 的值。

[CSS 自定义属性]: https://developer.mozilla.org/en-US/docs/Web/CSS/--*

因此，Sass 以与普通属性声明不同的方式解析自定义属性声明。所有标记，包括看起来像 SassScript 的标记，都会原样传递到 CSS。唯一的例外是 [插值][]，这是将动态值注入自定义属性的唯一方式。

[插值]: /documentation/interpolation

{% codeExample 'custom-properties' %}
  $primary: #81899b;
  $accent: #302e24;
  $warn: #dfa612;

  :root {
    --primary: #{$primary};
    --accent: #{$accent};
    --warn: #{$warn};

    // 尽管这看起来像一个 Sass 变量，但它是一个有效的 CSS，因此不会被评估。
    --consumed-by-js: $primary;
  }
  ===
  $primary: #81899b
  $accent: #302e24
  $warn: #dfa612

  :root
    --primary: #{$primary}
    --accent: #{$accent}
    --warn: #{$warn}

    // 尽管这看起来像一个 Sass 变量，但它是一个有效的 CSS，因此不会被评估。
    --consumed-by-js: $primary
{% endcodeExample %}

{% headsUp %}
  不幸的是，[插值][] 会移除字符串的引号，这使得在自定义属性中使用来自 Sass 变量的带引号字符串时变得困难。作为解决方法，您可以使用 [`meta.inspect()` 函数][] 来保留引号。

  [插值]: /documentation/interpolation
  [`meta.inspect()` 函数]: /documentation/modules/meta#inspect

  {% codeExample 'custom-properties-strings-meta' %}
    @use "sass:meta";

    $font-family-sans-serif: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto;
    $font-family-monospace: SFMono-Regular, Menlo, Monaco, Consolas;

    :root {
      --font-family-sans-serif: #{meta.inspect($font-family-sans-serif)};
      --font-family-monospace: #{meta.inspect($font-family-monospace)};
    }
    ===
    @use "sass:meta"

    $font-family-sans-serif: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto
    $font-family-monospace: SFMono-Regular, Menlo, Monaco, Consolas

    :root
      --font-family-sans-serif: #{meta.inspect($font-family-sans-serif)}
      --font-family-monospace: #{meta.inspect($font-family-monospace)}
  {% endcodeExample %}
{% endheadsUp %}

### `@function` 结果

{% compatibility 'dart: "1.94.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

[纯 CSS `@function` 规则] 的 `result` 属性类似于自定义属性：它也可以包含在属性值的任何位置，可以从 JavaScript 访问，并且可以采用几乎任何可能的值。Sass 以与自定义属性值相同的方式解析它，这意味着您必须使用插值将 SassScript 值包含在其中。

[纯 CSS `@function` 规则]: https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@function

{% codeExample 'plain-css-function' %}
  $highlight: #ddf;

  @function --highlight() {
    result: var(--highlight, #{$highlight});
  }
  ===
  $highlight: #ddf

  @function --highlight()
    result: var(--highlight, #{$highlight})
{% endcodeExample %}