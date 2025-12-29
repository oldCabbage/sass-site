---
title: '重大变更: 颜色函数'
introduction: >
  某些假设所有颜色都相互兼容的颜色函数在 Sass 支持 CSS Color 4 的所有颜色空间后已不再适用。
---

历史上，所有 Sass 颜色值覆盖相同的色域：无论颜色是以 RGB、HSL 或 HWB 定义的，它们仅覆盖 [the `sRGB` gamut] 并且只能表示自 1990 年代中期以来显示器能够显示的颜色。当 Sass 添加其原始的颜色函数集时，它们假设所有颜色可以在这些表示之间自由转换，并且每个通道名称（如“red”或“hue”）都有一个单一明确的含义。

[the `sRGB` gamut]: https://en.wikipedia.org/wiki/SRGB

[CSS Color 4] 的发布改变了这一切。它添加了对许多具有不同（更宽）色域的新颜色空间的支持，与 `sRGB` 不同。为了支持这些颜色，Sass 必须重新思考颜色函数的工作方式。除了添加新函数如 [`color.channel()`] 和 [`color.to-space()`] 之外，一些基于不再成立的假设的旧函数也被弃用。

[CSS Color 4]: https://developer.mozilla.org/en-US/blog/css-color-module-level-4/

[`color.channel()`]: /documentation/modules/color/#channel
[`color.to-space()`]: /documentation/modules/color/#to-space

### 旧的通道函数

通道名称现在在颜色空间之间是模糊的。传统的 RGB 空间有一个 `red` 通道，`display-p3`、`rec2020` 以及许多其他空间也是如此。这意味着 [`color.red()`], [`color.green()`], [`color.blue()`], [`color.hue()`], [`color.saturation()`], [`color.lightness()`], [`color.whiteness()`], [`color.blackness()`], [`color.alpha()`], 和 [`color.opacity()`] 将被移除。取而代之的是，您可以使用 [`color.channel()`] 函数来获取特定通道的值，通常使用显式的 `$space` 参数来指示您正在使用的颜色空间。

[`color.red()`]: /documentation/modules/color/#red
[`color.green()`]: /documentation/modules/color/#green
[`color.blue()`]: /documentation/modules/color/#blue
[`color.hue()`]: /documentation/modules/color/#hue
[`color.saturation()`]: /documentation/modules/color/#saturation
[`color.lightness()`]: /documentation/modules/color/#lightness
[`color.whiteness()`]: /documentation/modules/color/#whiteness
[`color.blackness()`]: /documentation/modules/color/#blackness
[`color.alpha()`]: /documentation/modules/color/#alpha
[`color.opacity()`]: /documentation/modules/color/#opacity

{% codeExample 'channel', false %}
  @use "sass:color";

  $color: #c71585;
  @debug color.channel($color, "red", $space: rgb);
  @debug color.channel($color, "red", $space: display-p3);
  @debug color.channel($color, "hue", $space: oklch);
  ===
  @use "sass:color"

  $color: #c71585
  @debug color.channel($color, "red", $space: rgb)
  @debug color.channel($color, "red", $space: display-p3)
  @debug color.channel($color, "hue", $space: oklch)
{% endcodeExample %}

### 单通道调整函数

这些函数与旧的通道函数具有相同的模糊性问题，而 _同时_ 在添加对 Color 4 的支持之前就已经因 [`color.adjust()`] 而变得多余。不仅如此，通常最好使用 [`color.scale()`]，因为它更适合相对于现有颜色进行更改而不是绝对更改。这意味着 [`adjust-hue()`], [`saturate()`], [`desaturate()`], [`lighten()`], [`darken()`], [`opacify()`], [`fade-in()`], [`transparentize()`], 和 [`fade-out()`] 将被移除。请注意，这些函数从未有过模块作用域的对应函数，因为它们的使用已经被禁止。

[`color.adjust()`]: /documentation/modules/color/#adjust
[`color.scale()`]: /documentation/modules/color/#scale
[`adjust-hue()`]: /documentation/modules/color/#adjust-hue
[`saturate()`]: /documentation/modules/color/#saturate
[`desaturate()`]: /documentation/modules/color/#desaturate
[`lighten()`]: /documentation/modules/color/#lighten
[`darken()`]: /documentation/modules/color/#darken
[`opacify()`]: /documentation/modules/color/#opacify
[`fade-in()`]: /documentation/modules/color/#fade-in
[`transparentize()`]: /documentation/modules/color/#transparentize
[`fade-out()`]: /documentation/modules/color/#fade-out

{% codeExample 'adjust', false %}
  @use "sass:color";

  $color: #c71585;
  @debug color.adjust($color, $lightness: 15%, $space: hsl);
  @debug color.adjust($color, $lightness: 15%, $space: oklch);
  @debug color.scale($color, $lightness: 15%, $space: oklch);
  ===
  @use "sass:color"

  $color: #c71585
  @debug color.adjust($color, $lightness: 15%, $space: hsl)
  @debug color.adjust($color, $lightness: 15%, $space: oklch)
  @debug color.scale($color, $lightness: 15%, $space: oklch)
{% endcodeExample %}

## 过渡期

{% compatibility 'dart: "1.79.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

首先，我们将为所有计划被移除的函数发出弃用警告。在 Dart Sass 2.0.0 中，这些函数将被完全移除。尝试调用模块作用域版本将引发错误，而全局函数将被视为纯 CSS 函数并作为纯字符串发出。

您可以使用 [the Sass migrator] 自动从弃用的 API 迁移到它们的新替代品。

[the Sass migrator]: https://sass-lang.com/documentation/cli/migrator/#color

{% render 'silencing_deprecations' %}