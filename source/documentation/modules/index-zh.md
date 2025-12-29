---
title: 内置模块
eleventyComputed:
  before_introduction: >
    {% render 'doc_snippets/built-in-module-status' %}
introduction: >
  Sass 提供了许多内置模块，其中包含有用的功能（和偶尔的混合宏）。这些模块可以像任何用户定义的样式表一样使用 [`@use`
  规则](/documentation/at-rules/use) 加载，其功能可以像任何其他模块成员一样调用 [像任何其他模块
  成员](/documentation/at-rules/use#loading-members)。所有内置模块的 URL 都以 `sass:` 开头，以指示它们是 Sass 自身的一部分。
---

{% headsUp %}
  在引入 Sass 模块系统之前，所有 Sass 函数都是全局可用的。许多函数仍然具有全局别名（这些在它们的文档中列出）。Sass 团队不鼓励使用这些别名，并且最终会弃用它们，但目前为了与旧版本的 Sass 和 LibSass（尚不支持模块系统）兼容，它们仍然可用。

  [一些函数][] *仅* 在新的模块系统中全局可用，因为它们在内置的 CSS 函数之上添加了额外的行为。

  [一些函数]: #global-functions
  [`rgb()`]: #rgb
  [`hsl()`]: #hsl
{% endheadsUp %}

{% codeExample 'modules' %}
  @use "sass:color";

  .button {
    $primary-color: #6b717f;
    color: $primary-color;
    border: 1px solid color.scale($primary-color, $lightness: 20%);
  }
  ===
  @use "sass:color"

  .button
    $primary-color: #6b717f
    color: $primary-color
    border: 1px solid color.scale($primary-color, $lightness: 20%)
{% endcodeExample %}

Sass 提供了以下内置模块：

* [`sass:math` 模块][] 提供了对 [数字][] 进行操作的功能。

* [`sass:string` 模块][] 使组合、搜索或拆分 [字符串][] 变得容易。

* [`sass:color` 模块][] 根据现有颜色生成新的 [颜色][]，使构建颜色主题变得容易。

* [`sass:list` 模块][] 让您可以访问和修改 [列表][] 中的值。

* [`sass:map` 模块][] 使您可以查找 [映射][] 中与键关联的值，等等。

* [`sass:selector` 模块][] 提供了对 Sass 强大的选择器引擎的访问。

* [`sass:meta` 模块][] 暴露了 Sass 内部工作的细节。

[`sass:math` 模块]: /documentation/modules/math
[数字]: /documentation/values/numbers
[`sass:string` 模块]: /documentation/modules/string
[字符串]: /documentation/values/strings
[`sass:color` 模块]: /documentation/modules/color
[颜色]: /documentation/values/colors
[`sass:list` 模块]: /documentation/modules/list
[列表]: /documentation/values/lists
[`sass:map` 模块]: /documentation/modules/map
[映射]: /documentation/values/maps
[`sass:selector` 模块]: /documentation/modules/selector
[`sass:meta` 模块]: /documentation/modules/meta

## 全局函数

{% funFact %}
  您可以将 [特殊函数] 如 `calc()` 或 `var()` 传递给任何全局颜色构造函数的参数。您甚至可以使用 `var()` 代替多个参数，因为它可能会被多个值替换！当以这种方式调用颜色函数时，它将返回一个使用与调用时相同的签名的非引用字符串。

  [特殊函数]: /documentation/syntax/special-functions

  {% codeExample 'color-special', false %}
    @debug rgb(0 51 102 / var(--opacity)); // rgb(0 51 102 / var(--opacity))
    @debug color(display-p3 var(--peach)); // color(display-p3 var(--peach))
    ===
    @debug rgb(0 51 102 / var(--opacity))  // rgb(0 51 102 / var(--opacity))
    @debug color(display-p3 var(--peach))  // color(display-p3 var(--peach))
  {% endcodeExample %}
{% endfunFact %}

{% function 'color($space $channel1 $channel2 $channel3)', 'color($space $channel1 $channel2 $channel3 / $alpha)', 'returns:color' %}
  {% compatibility 'dart: "1.78.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回给定颜色空间和通道值的颜色。

  这支持颜色空间 `srgb`、`srgb-linear`、`display-p3`、`a98-rgb`、`prophoto-rgb`、`rec2020`、`xyz` 和 `xyz-d50`，以及 `xyz-d65` 作为 `xyz` 的别名。对于所有空间，通道是 0 到 1（包括）之间的数字或 `0%` 到 `100%`（包括）之间的百分比。
  
  如果任何颜色通道超出 0 到 1 的范围，则表示该颜色超出了其颜色空间的标准色域。

  {% codeExample 'hsl', false %}
    @debug color(srgb 0.1 0.6 1); // color(srgb 0.1 0.6 1)
    @debug color(xyz 30% 0% 90% / 50%); // color(xyz 0.3 0 0.9 / 50%)
    ===
    @debug color(srgb 0.1 0.6 1)  // color(srgb 0.1 0.6 1)
    @debug color(xyz 30% 0% 90% / 50%)  // color(xyz 0.3 0 0.9 / 50%)
  {% endcodeExample %}
{% endfunction %}

{% function 'hsl($hue $saturation $lightness)', 'hsl($hue $saturation $lightness / $alpha)', 'hsl($hue, $saturation, $lightness, $alpha: 1)', 'hsla($hue $saturation $lightness)', 'hsla($hue $saturation $lightness / $alpha)', 'hsla($hue, $saturation, $lightness, $alpha: 1)', 'returns:color' %}
  {% compatibility 'dart: "1.15.0"', 'libsass: false', 'ruby: false', 'feature: "Level 4 Syntax"' %}
    LibSass 和 Ruby Sass 仅支持以下签名：

    * `hsl($hue, $saturation, $lightness)`
    * `hsla($hue, $saturation, $lightness, $alpha)`

    请注意，对于这些实现，如果函数名称为 `hsla()`，则 `$alpha` 参数是 *必需的*，如果函数名称为 `hsl()`，则 `$alpha` 参数是 *禁止的*。
  {% endcompatibility %}

  {% compatibility 'dart: true', 'libsass: false', 'ruby: "3.7.0"', 'feature: "Percent Alpha"' %}
    LibSass 和较早版本的 Ruby Sass 不支持以百分比指定的 alpha 值。
  {% endcompatibility %}

  返回具有给定 [色相、饱和度和亮度][] 以及给定 alpha 通道的颜色。

  [色相、饱和度和亮度]: https://en.wikipedia.org/wiki/HSL_and_HSV

  色相是一个介于 `0deg` 和 `360deg`（包括）之间的数字，可以无单位。饱和度和亮度通常是介于 `0%` 和 `100%`（包括）之间的数字，并且 *不能* 无单位。alpha 通道可以指定为介于 0 和 1（包括）之间的无单位数字，或介于 `0%` 和 `100%`（包括）之间的百分比。

  超出 `0deg` 和 `360deg` 的色相等同于 `$hue % 360deg`。小于 `0%` 的饱和度将被限制为 `0%`。高于 `100%` 的饱和度或超出 `0%` 和 `100%` 的亮度都是允许的，并表示超出标准 RGB 色域的颜色。

  {% headsUp %}
    Sass 对斜杠分隔值的 [特殊解析规则][] 使得在使用 `hsl($hue $saturation $lightness / $alpha)` 签名时传递 `$lightness` 或 `$alpha` 变量变得困难。请考虑改用 `hsl($hue, $saturation, $lightness, $alpha)`。

    [特殊解析规则]: /documentation/operators/numeric#slash-separated-values
  {% endheadsUp %}

  {% codeExample 'hsl', false %}
    @debug hsl(210deg 100% 20%); // #036
    @debug hsl(210deg 100% 20% / 50%); // rgba(0, 51, 102, 0.5)
    @debug hsla(34, 35%, 92%, 0.2); // rgba(241.74, 235.552, 227.46, 0.2)
    ===
    @debug hsl(210deg 100% 20%) // #036
    @debug hsl(210deg 100% 20% / 50%)  // rgba(0, 51, 102, 0.5)
    @debug hsla(34, 35%, 92%, 0.2)  // rgba(241.74, 235.552, 227.46, 0.2)
  {% endcodeExample %}
{% endfunction %}

{% function 'hwb($hue $whiteness $blackness)', 'hwb($hue $whiteness $blackness / $alpha)', 'color.hwb($hue $whiteness $blackness)', 'color.hwb($hue $whiteness $blackness / $alpha)', 'color.hwb($hue, $whiteness, $blackness, $alpha: 1)', 'returns:color' %}
  {% compatibility 'dart: "1.78.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回具有给定 [色相、白度和黑度][] 以及给定 alpha 通道的颜色。

  [色相、白度和黑度]: https://en.wikipedia.org/wiki/HWB_color_model

  色相是一个介于 `0deg` 和 `360deg`（包括）之间的数字，可以无单位。白度和黑度通常是介于 `0%` 和 `100%`（包括）之间的数字，并且 *不能* 无单位。alpha 通道可以指定为介于 0 和 1（包括）之间的无单位数字，或介于 `0%` 和 `100%`（包括）之间的百分比。

  超出 `0deg` 和 `360deg` 的色相等同于 `$hue % 360deg`。如果 `$whiteness + $blackness > 100%`，则两个值将被缩放，使其相加等于 `100%`。如果 `$whiteness`、`$blackness` 或两者都小于 `0%`，则表示超出标准 RGB 色域的颜色。
  
  {% headsUp %}
    `color.hwb()` 变体已被弃用。新的 Sass 代码应使用全局 `hwb()` 函数。
  {% endheadsUp %}

  {% codeExample 'hwb', false %}
    @debug hwb(210deg 0% 60%); // #036
    @debug hwb(210 0% 60% / 0.5); // rgba(0, 51, 102, 0.5)
    ===
    @debug hwb(210deg 0% 60%)  // #036
    @debug hwb(210 0% 60% / 0.5)  // rgba(0, 51, 102, 0.5)
  {% endcodeExample %}
{% endfunction %}

{% function 'lab($lightness $a $b)', 'lab($lightness $a $b / $alpha)', 'returns:color' %}
  {% compatibility 'dart: "1.78.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回具有给定 [亮度、a、b] 和 alpha 通道的颜色。

  [色相、白度和黑度]: https://en.wikipedia.org/wiki/CIELAB_color_space

  亮度是一个介于 `0%` 和 `100%`（包括）之间的数字，可以无单位。a 和 b 通道可以指定为介于 -125 和 125（包括）之间的 [无单位] 数字，或介于 `-100%` 和 `100%`（包括）之间的百分比。alpha 通道可以指定为介于 0 和 1（包括）之间的无单位数字，或介于 `0%` 和 `100%`（包括）之间的百分比。

  [无单位]: /documentation/values/numbers#units

  超出 `0%` 和 `100%` 范围的亮度将被限制在该范围内。如果 a 或 b 通道超出 -125 到 125 的范围，则表示超出标准 CIELAB 色域的颜色。

  {% codeExample 'lab', false %}
    @debug lab(50% -20 30); // lab(50% -20 30)
    @debug lab(80% 0% 20% / 0.5); // lab(80% 0 25 / 0.5);
    ===
    @debug lab(50% -20 30)  // lab(50% -20 30)
    @debug lab(80% 0% 20% / 0.5)  // lab(80% 0 25 / 0.5);
  {% endcodeExample %}
{% endfunction %}

{% function 'lch($lightness $chroma $hue)', 'lch($lightness $chroma $hue / $alpha)', 'returns:color' %}
  {% compatibility 'dart: "1.78.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回具有给定 [亮度、色度和色相] 以及给定 alpha 通道的颜色。

  [色相、白度和黑度]: https://en.wikipedia.org/wiki/CIELAB_color_space#Cylindrical_model

  亮度是一个介于 `0%` 和 `100%`（包括）之间的数字，可以无单位。色度通道可以指定为介于 0 和 150（包括）之间的 [无单位] 数字，或介于 `0%` 和 `100%`（包括）之间的百分比。色相是一个介于 `0deg` 和 `360deg`（包括）之间的数字，可以无单位。alpha 通道可以指定为介于 0 和 1（包括）之间的无单位数字，或介于 `0%` 和 `100%`（包括）之间的百分比。

  [无单位]: /documentation/values/numbers#units

  超出 `0%` 和 `100%` 范围的亮度将被限制在该范围内。色度低于 0 将被限制为 0，色度高于 150 表示超出标准 CIELAB 色域的颜色。超出 `0deg` 和 `360deg` 的色相等同于 `$hue % 360deg`。

  {% codeExample 'lch', false %}
    @debug lch(50% 10 270deg); // lch(50% 10 270deg)
    @debug lch(80% 50% 0.2turn / 0.5); // lch(80% 75 72deg / 0.5);
    ===
    @debug lch(50% 10 270deg)  // lch(50% 10 270deg)
    @debug lch(80% 50% 0.2turn / 0.5)  // lch(80% 75 72deg / 0.5);
  {% endcodeExample %}
{% endfunction %}

{% function 'oklab($lightness $a $b)', 'oklab($lightness $a $b / $alpha)', 'returns:color' %}
  {% compatibility 'dart: "1.78.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回具有给定 [感知均匀亮度、a、b] 和 alpha 通道的颜色。

  [感知均匀亮度、a、b]: https://bottosson.github.io/posts/oklab/

  亮度是一个介于 `0%` 和 `100%`（包括）之间的数字，可以无单位。a 和 b 通道可以指定为介于 -0.4 和 0.4（包括）之间的 [无单位] 数字，或介于 `-100%` 和 `100%`（包括）之间的百分比。alpha 通道可以指定为介于 0 和 1（包括）之间的无单位数字，或介于 `0%` 和 `100%`（包括）之间的百分比。

  [无单位]: /documentation/values/numbers#units

 