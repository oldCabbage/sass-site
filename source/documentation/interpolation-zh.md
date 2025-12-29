---
title: 插值
table_of_contents: true
introduction: >
  插值可以在 Sass 样式表的几乎所有地方使用，以将 [SassScript 表达式](/documentation/syntax/structure#expressions) 的结果嵌入到一段 CSS 中。
  只需在以下任何位置将表达式用 `#{}` 包裹：
---

* [样式规则中的选择器](/documentation/style-rules#interpolation)
* [声明中的属性名](/documentation/style-rules/declarations#interpolation)
* [自定义属性值](/documentation/style-rules/declarations#custom-properties)
* [CSS at-rules](/documentation/at-rules/css)
* [`@extend`](/documentation/at-rules/extend)
* [普通 CSS `@import`](/documentation/at-rules/import/#plain-css-imports)
* [带引号或不带引号的字符串](/documentation/values/strings)
* [特殊函数](/documentation/syntax/special-functions)
* [普通 CSS 函数名](/documentation/at-rules/function/#plain-css-functions)
* [loud 注释](/documentation/syntax/comments)

{% codeExample 'interpolation' %}
  @mixin corner-icon($name, $top-or-bottom, $left-or-right) {
    .icon-#{$name} {
      background-image: url("/icons/#{$name}.svg");
      position: absolute;
      #{$top-or-bottom}: 0;
      #{$left-or-right}: 0;
    }
  }

  @include corner-icon("mail", top, left);
  ===
  @mixin corner-icon($name, $top-or-bottom, $left-or-right)
    .icon-#{$name}
      background-image: url("/icons/#{$name}.svg")
      position: absolute
      #{$top-or-bottom}: 0
      #{$left-or-right}: 0



  @include corner-icon("mail", top, left)
{% endcodeExample %}

## 在 SassScript 中

{% compatibility 'dart: true', 'libsass: false', 'ruby: "4.0.0 (unreleased)"', 'feature: "Modern Syntax"' %}
  LibSass 和 Ruby Sass 当前使用一种较旧的语法来解析 SassScript 中的插值。从大多数实际用途来看，它的行为相同，但在 [运算符][] 周围可能会表现出奇怪的行为。有关详细信息，请参阅 [此文档][]。

  [operators]: /documentation/operators
  [this document]: https://github.com/sass/sass/blob/main/accepted/free-interpolation.md#old-interpolation-rules
{% endcompatibility %}

插值可以在 SassScript 中用于将 SassScript 注入到 [不带引号的字符串][] 中。这在动态生成名称（例如动画）或使用 [斜杠分隔的值][] 时特别有用。请注意，SassScript 中的插值始终返回不带引号的字符串。

[unquoted strings]: /documentation/values/strings#unquoted
[slash-separated values]: /documentation/operators/numeric#slash-separated-values

<!-- Add explicit CSS here to prevent diffs due to the use of unique-id -->

{% codeExample 'interpolation-sass-script' %}
  @mixin inline-animation($duration) {
    $name: inline-#{unique-id()};

    @keyframes #{$name} {
      @content;
    }

    animation-name: $name;
    animation-duration: $duration;
    animation-iteration-count: infinite;
  }

  .pulse {
    @include inline-animation(2s) {
      from { background-color: yellow }
      to { background-color: red }
    }
  }
  ===
  @mixin inline-animation($duration)
    $name: inline-#{unique-id()}

    @keyframes #{$name}
      @content


    animation-name: $name
    animation-duration: $duration
    animation-iteration-count: infinite


  .pulse
    @include inline-animation(2s)
      from
        background-color: yellow
      to
        background-color: red
  ===
  .pulse {
    animation-name: inline-uifpe6h;
    animation-duration: 2s;
    animation-iteration-count: infinite;
  }
  @keyframes inline-uifpe6h {
    from {
      background-color: yellow;
    }
    to {
      background-color: red;
    }
  }
{% endcodeExample %}

{% funFact %}
  插值用于将值注入字符串中很有用，但在 SassScript 表达式中很少需要它。你绝对不需要在属性值中使用变量时使用它。与其写 `color: #{$accent}`，你可以直接写 `color: $accent`！
{% endfunFact %}

{% headsUp %}
  几乎总是不建议在数字中使用插值。插值返回的不带引号的字符串不能用于进一步的数学运算，并且会避开 Sass 内置的保护机制，以确保正确使用单位。

  Sass 具有强大的 [单位算术][]，你可以使用它来代替。例如，与其写 `#{$width}px`，写 `$width * 1px` —— 更好的是，一开始就将 `$width` 变量声明为 `px`。这样，如果 `$width` 已经有单位，你将得到一个有用的错误消息，而不是编译出无效的 CSS。

  [unit arithmetic]: /documentation/values/numbers#units
{% endheadsUp %}

## 带引号的字符串

在大多数情况下，插值会注入与将表达式用作 [属性值][] 时相同的确切文本。但有一个例外：带引号字符串周围的引号会被移除（即使这些带引号的字符串在列表中）。这使得可以编写包含 SassScript 中不允许的语法（如选择器）的带引号字符串，并将它们插值到样式规则中。

[property value]: /documentation/style-rules/declarations

{% codeExample 'quoted-strings' %}
  .example {
    unquoted: #{"string"};
  }
  ===
  .example
    unquoted: #{"string"}
{% endcodeExample %}

{% headsUp %}
  尽管使用此功能将带引号的字符串转换为不带引号的字符串很有诱惑力，但使用 [`string.unquote()` 函数][] 更加清晰。与其写 `#{$string}`，写 `string.unquote($string)`！

  [`string.unquote()` 函数]: /documentation/modules/string#unquote
{% endheadsUp %}
