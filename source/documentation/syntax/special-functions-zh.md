---
title: 特殊函数
table_of_contents: true
introduction: >
  CSS 定义了许多函数，其中大多数都可以与 Sass 的正常函数语法一起正常工作。它们被解析为函数调用，解析为 [纯 CSS 函数](/documentation/at-rules/function/#plain-css-functions)，并按原样编译为 CSS。不过，有一些例外情况，这些情况具有特殊的语法，不能仅解析为 [SassScript 表达式](/documentation/syntax/structure#expressions)。所有特殊函数调用都返回 [非引号字符串](/documentation/values/strings#unquoted)。
---

## `if()`

{% compatibility 'dart: "1.95.0"', 'libsass: false', 'ruby: false', 'feature: "calc()"' %}
  LibSass、Ruby Sass 和 Dart Sass 1.95.0 之前的版本将 `if()` 解析为具有签名 `if($condition, $if-true, $if-false)` 的 Sass 函数。如果 `$condition` 为 [真值]，此函数返回 `$if-true`；否则，返回 `$if-false`。此函数具有特殊的语法，可以避免评估与 `$condition` 不匹配的分支。

  [真值]: /documentation/at-rules/control/if#truthiness-and-falsiness

  Dart Sass 1.95.0 及更高版本按以下方式解析 `if()`。Dart Sass 3.0.0 之前的版本仍然支持旧的 `if()` 语法，但已视为已弃用。请参阅 [/d/if-function]。

  [/d/if-function]: /documentation/breaking-changes/if-function
{% endcompatibility %}

Sass 支持 [CSS `if()` 函数]，并添加了一个重要功能：`sass(...)` 条件，该条件接受一个 SassScript 表达式，并在该表达式计算结果为 [真值] 时匹配。仅包含 `sass(...)` 条件（和可选的 `else`）的 `if()` 函数将完全由 Sass 评估，并返回相应的值。

[CSS `if()` 函数]: https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/if
[真值]: /documentation/at-rules/control/if#truthiness-and-falsiness

SassScript 在 `if()` 函数的条件中 *仅* 允许在 `sass(...)` 条件内或在 [插值] 中使用。另一方面，值是正常的 SassScript 表达式，不需要任何特殊的包装。只有匹配条件的值会被评估，因此其他值可以引用不存在的变量或调用会导致错误的函数。

如果纯 Sass `if()` 中没有条件匹配，则返回 `null`。

[插值]: /documentation/interpolation

{% codeExample 'css-if-sass' %}
  @use 'sass:meta';

  $hungry: true;
  @debug if(sass($hungry): breakfast burrito; else: cereal); // breakfast burrito

  // 您可以使用 CSS 布尔表达式与 sass(...) 条件。
  @debug if(not sass($hungry): skip lunch); // null

  // 仅评估匹配的分支。
  @debug if(sass(meta.variable-exists("thirsty")): thirsty; else: hungry); // hungry
  ===
  @use 'sass:meta'

  $hungry: true
  @debug if(sass($hungry): breakfast burrito; else: cereal)  // breakfast burrito

  // 您可以使用 CSS 布尔表达式与 sass(...) 条件。
  @debug if(not sass($hungry): skip lunch)  // null

  // 仅评估匹配的分支。
  @debug if(sass(meta.variable-exists("thirsty")): thirsty; else: hungry)  // hungry
{% endcodeExample %}

`sass(...)` 条件也可以与普通的 CSS 条件结合使用。Sass 条件将由 Sass 评估，但如果存在任何 CSS 条件，Sass 将返回整个结果作为字符串。

{% codeExample 'css-if-mixed' %}
  $support-widescreen: true;
  @debug if(
    sass($support-widescreen) and media(width >= 3000px): big;
    else: small
  ); // if(media(width >= 3000px): big; else: small)

  // 如果 Sass 条件意味着某个分支将永远不会匹配（或总是匹配），Sass
  // 会尽早移除该分支，并尽可能返回最终值。
  $support-widescreen: false;
  @debug if(
    sass($support-widescreen) and media(width >= 3000px): big;
    else: small
  ); // small
  ===
  $support-widescreen: true
  @debug if(
    sass($support-widescreen) and media(width >= 3000px): big;
    else: small
  )  // if(media(width >= 3000px): big; else: small)

  // 如果 Sass 条件意味着某个分支将永远不会匹配（或总是匹配），Sass
  // 会尽早移除该分支，并尽可能返回最终值。
  $support-widescreen: false
  @debug if(
    sass($support-widescreen) and media(width >= 3000px): big;
    else: small
  )  // small
{% endcodeExample %}

## `url()`

[`url()` 函数][] 在 CSS 中常用，但其语法与其他函数不同：它可以接受带引号或不带引号的 URL。因为不带引号的 URL 不是有效的 SassScript 表达式，Sass 需要特殊的逻辑来解析它。

[`url()` 函数]: https://developer.mozilla.org/en-US/docs/Web/CSS/url

如果 `url()` 的参数是有效的不带引号的 URL，Sass 按原样解析它，尽管可以使用 [插值] 注入 SassScript 值。如果不是有效的不带引号的 URL（例如，如果包含 [变量][] 或 [函数调用][]），则解析为正常的 [纯 CSS 函数调用][]。

[插值]: /documentation/interpolation
[变量]: /documentation/variables
[函数调用]: /documentation/at-rules/function
[纯 CSS 函数调用]: /documentation/at-rules/function/#plain-css-functions

{% codeExample 'url' %}
  $roboto-font-path: "../fonts/roboto";

  @font-face {
      // 这被解析为一个正常的函数调用，接受带引号的字符串。
      src: url("#{$roboto-font-path}/Roboto-Thin.woff2") format("woff2");

      font-family: "Roboto";
      font-weight: 100;
  }

  @font-face {
      // 这被解析为一个正常的函数调用，接受算术表达式。
      src: url($roboto-font-path + "/Roboto-Light.woff2") format("woff2");

      font-family: "Roboto";
      font-weight: 300;
  }

  @font-face {
      // 这被解析为一个插值的特殊函数。
      src: url(#{$roboto-font-path}/Roboto-Regular.woff2) format("woff2");

      font-family: "Roboto";
      font-weight: 400;
  }
  ===
  $roboto-font-path: "../fonts/roboto"

  @font-face
      // 这被解析为一个正常的函数调用，接受带引号的字符串。
      src: url("#{$roboto-font-path}/Roboto-Thin.woff2") format("woff2")

      font-family: "Roboto"
      font-weight: 100


  @font-face
      // 这被解析为一个正常的函数调用，接受算术表达式。
      src: url($roboto-font-path + "/Roboto-Light.woff2") format("woff2")

      font-family: "Roboto"
      font-weight: 300


  @font-face
      // 这被解析为一个插值的特殊函数。
      src: url(#{$roboto-font-path}/Roboto-Regular.woff2) format("woff2")

      font-family: "Roboto"
      font-weight: 400
{% endcodeExample %}

## `element()`、`progid:...()` 和 `expression()`

{% compatibility 'dart: "1.40.0"', 'libsass: false', 'ruby: false', 'feature: "calc()"' %}
  LibSass、Ruby Sass 和 Dart Sass 1.40.0 之前的版本将 `calc()` 解析为类似于 `element()` 的特殊语法函数。

  Dart Sass 1.40.0 及更高版本将 `calc()` 解析为 [计算值]。

  [计算值]: /documentation/values/calculations
{% endcompatibility %}

{% compatibility 'dart: ">=1.31.0 <1.40.0"', 'libsass: false', 'ruby: false', 'feature: "clamp()"' %}
  LibSass、Ruby Sass 和 Dart Sass 1.31.0 之前的版本将 `clamp()` 解析为 [纯 CSS 函数]，而不是支持其内部的特殊语法。

  [纯 CSS 函数]: /documentation/at-rules/function/#plain-css-functions

  Dart Sass 1.31.0 到 1.40.0 之间的版本将 `clamp()` 解析为类似于 `element()` 的特殊语法函数。

  Dart Sass 1.40.0 及更高版本将 `clamp()` 解析为 [计算值]。

  [计算值]: /documentation/values/calculations
{% endcompatibility %}

[`element()`] 函数在 CSS 规范中定义，因为其 ID 可能被解析为颜色，因此需要特殊的解析。

[`element()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/element

[`expression()`][] 和以 [`progid:`][] 开头的函数是旧版 Internet Explorer 的特性，使用非标准语法。尽管它们不再被现代浏览器支持，Sass 为了向后兼容仍然解析它们。

[`expression()`]:
    https://blogs.msdn.microsoft.com/ie/2008/10/16/ending-expressions/
[`progid:`]:
    https://blogs.msdn.microsoft.com/ie/2009/02/19/the-css-corner-using-filters-in-ie8/

Sass 允许这些函数调用中包含 *任何文本*，包括嵌套的括号。除了 [插值] 可以用于注入动态值之外，没有任何内容会被解析为 SassScript 表达式。

[插值]: /documentation/interpolation

{% codeExample 'element' %}
  $logo-element: logo-bg;

  .logo {
    background: element(##{$logo-element});
  }
  ===
  $logo-element: logo-bg

  .logo
    background: element(##{$logo-element})
{% endcodeExample %}