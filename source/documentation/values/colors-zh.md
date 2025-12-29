---
title: 颜色
table_of_contents: true
---

{% compatibility 'dart: "1.79.0"', 'libsass: false', 'ruby: false', 'feature: "Color Spaces"' %}
  LibSass, Ruby Sass 以及 Dart Sass 的旧版本不支持 `rgb` 和 `hsl` 之外的颜色空间。

  除了添加对新颜色空间的支持外，此版本还更改了一些颜色处理的细节。特别是，即使是传统的 `rgb` 和 `hsl` 颜色空间也不再被限制在其色域内；现在可以表示 `rgb(500 0 0)` 或其他超出范围的值。此外，`rgb` 颜色不再四舍五入到最接近的整数，因为 CSS 规范现在要求实现尽可能保持精度。
{% endcompatibility %}

{% compatibility 'dart: "1.14.0"', 'libsass: false', 'ruby: "3.6.0"', 'feature: "Level 4 Syntax"' %}
  LibSass 和 Dart 或 Ruby Sass 的旧版本不支持 [带 alpha 通道的十六进制颜色][]。

  [带 alpha 通道的十六进制颜色]: https://drafts.csswg.org/css-color/#hex-notation
{% endcompatibility %}

Sass 内置了对颜色值的支持。就像 CSS 颜色一样，每个颜色都代表特定颜色空间中的一个点，例如 `rgb` 或 `lab`。Sass 颜色可以写成十六进制代码（`#f2ece4` 或 `#b37399aa`），[CSS 颜色名称]（`midnightblue`, `transparent`），或颜色函数如 [`rgb()`], [`lab()`], 或 [`color()`]。

[sRGB 颜色空间]: https://en.wikipedia.org/wiki/SRGB
[颜色函数]: /documentation/modules/color
[CSS 颜色名称]: https://developer.mozilla.org/en-US/docs/Web/CSS/color_value#Color_keywords
[`rgb()`]: /documentation/modules#rgb
[`lab()`]: /documentation/modules#lab
[`color()`]: /documentation/modules#color

{% codeExample 'colors', false %}
  @debug #f2ece4; // #f2ece4
  @debug #b37399aa; // rgba(179, 115, 153, 67%)
  @debug midnightblue; // #191970
  @debug rgb(204 102 153); // #c69
  @debug lab(32.4% 38.4 -47.7 / 0.7); // lab(32.4% 38.4 -47.7 / 0.7)
  @debug color(display-p3 0.597 0.732 0.576); // color(display-p3 0.597 0.732 0.576)
  ===
  @debug #f2ece4  // #f2ece4
  @debug #b37399aa  // rgba(179, 115, 153, 67%)
  @debug midnightblue  // #191970
  @debug rgb(204 102 153)  // #c69
  @debug lab(32.4% 38.4 -47.7 / 0.7)  // lab(32.4% 38.4 -47.7 / 0.7)
  @debug color(display-p3 0.597 0.732 0.576)  // color(display-p3 0.597 0.732 0.576)
{% endcodeExample %}

## 颜色空间

Sass 支持与 CSS 相同的颜色空间。Sass 颜色将始终以写入时的颜色空间发出，除非它处于 [传统颜色空间] 或者你使用 [`color.to-space()`] 将其转换为另一个空间。Sass 中的所有其他颜色函数将始终返回与原始颜色相同空间的颜色，即使该函数在另一个空间中对颜色进行了更改。

[传统颜色空间]: #legacy-color-spaces
[`color.to-space()`]: /documentation/modules/color#to-space

尽管每个颜色空间对其通道都有预期的色域范围，但 Sass 可以为任何颜色空间表示超出色域的值。这使得宽色域空间中的颜色可以安全地转换为并转换回窄色域空间，而不会丢失信息。

{% headsUp %}
  CSS 要求某些颜色函数剪辑其输入通道。例如，`rgb(500 0 0)` 将其红色通道剪辑为在 [0, 255] 范围内，因此即使 `rgb(500 0 0)` 是 Sass 可以表示的唯一值，它也等同于 `rgb(255 0 0)`。你可以使用 Sass 的 [`color.change()`] 函数为任何空间设置超出色域的值。

  [`color.change()`]: /documentation/modules/color#change
{% endheadsUp %}

以下是 Sass 支持的所有颜色空间的完整列表。你可以在 [MDN] 上了解这些空间。

{% compatibility 'dart: "1.97.0"', 'libsass: false', 'ruby: false', 'feature: "display-p3-linear"' %}
{% endcompatibility %}

[on MDN]: https://developer.mozilla.org/en-US/docs/Glossary/Color_space

<table class="sl-c-table">
  <tr>
    <th scope="col">Space</th>
    <th scope="col">Syntax</th>
    <th scope="col">Channels [min, max]</th>
  </tr>
  <tr>
    <th scope="row"><code>rgb</code>*</th>
    <td>
      <code>rgb(102 51 153)</code><br>
      <code>#663399</code><br>
      <code>rebeccapurple</code>
    </td>
    <td>
      red <span class="fade">[0, 255]</span>;
      green <span class="fade">[0, 255]</span>;
      blue <span class="fade">[0, 255]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>hsl</code>*</th>
    <td><code>hsl(270 50% 40%)</code></td>
    <td>
      hue <span class="fade">[0, 360]</span>;
      saturation <span class="fade">[0%, 100%]</span>;
      lightness <span class="fade">[0%, 100%]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>hwb</code>*</th>
    <td><code>hwb(270 20% 40%)</code></td>
    <td>
      hue <span class="fade">[0, 360]</span>;
      whiteness <span class="fade">[0%, 100%]</span>;
      blackness <span class="fade">[0%, 100%]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>srgb</code></th>
    <td><code>color(srgb 0.4 0.2 0.6)</code></td>
    <td>
      red <span class="fade">[0, 1]</span>;
      green <span class="fade">[0, 1]</span>;
      blue <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>srgb-linear</code></th>
    <td><code>color(srgb-linear 0.133 0.033 0.319)</code></td>
    <td>
      red <span class="fade">[0, 1]</span>;
      green <span class="fade">[0, 1]</span>;
      blue <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>display-p3</code></th>
    <td><code>color(display-p3 0.1154 0.0363 0.2946)</code></td>
    <td>
      red <span class="fade">[0, 1]</span>;
      green <span class="fade">[0, 1]</span>;
      blue <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>display-p3-linear</code></th>
    <td><code>color(display-p3-linear 0.374 0.21 0.579)</code></td>
    <td>
      red <span class="fade">[0, 1]</span>;
      green <span class="fade">[0, 1]</span>;
      blue <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>a98-rgb</code></th>
    <td><code>color(a98-rgb 0.358 0.212 0.584)</code></td>
    <td>
      red <span class="fade">[0, 1]</span>;
      green <span class="fade">[0, 1]</span>;
      blue <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>prophoto-rgb</code></th>
    <td><code>color(prophoto-rgb 0.316 0.191 0.495)</code></td>
    <td>
      red <span class="fade">[0, 1]</span>;
      green <span class="fade">[0, 1]</span>;
      blue <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>rec2020</code></th>
    <td><code>color(rec2020 0.305 0.168 0.531)</code></td>
    <td>
      red <span class="fade">[0, 1]</span>;
      green <span class="fade">[0, 1]</span>;
      blue <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>xyz</code>, <code>xyz-d65</code></th>
    <td>
      <code>color(xyz 0.124 0.075 0.309)</code><br>
      <code>color(xyz-d65 0.124 0.075 0.309)</code>
    </td>
    <td>
      x <span class="fade">[0, 1]</span>;
      y <span class="fade">[0, 1]</span>;
      z <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>xyz-d50</code></th>
    <td><code>color(xyz-d50 0.116 0.073 0.233)</code></td>
    <td>
      x <span class="fade">[0, 1]</span>;
      y <span class="fade">[0, 1]</span>;
      z <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>lab</code></th>
    <td><code>lab(32.4% 38.4 -47.7)</code></td>
    <td>
      lightness <span class="fade">[0%, 100%]</span>;
      a <span class="fade">[-125, 125]</span>;
      b <span class="fade">[-125, 125]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>lch</code></th>
    <td><code>lch(32.4% 61.2 308.9deg)</code></td>
    <td>
      lightness <span class="fade">[0%, 100%]</span>;
      chroma <span class="fade">[0, 150]</span>;
      hue <span class="fade">[0deg, 360deg]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>oklab</code></th>
    <td><code>oklab(44% 0.088 -0.134)</code></td>
    <td>
      lightness <span class="fade">[0%, 100%]</span>;
      a <span class="fade">[-0.4, 0.4]</span>;
      b <span class="fade">[-0.4, 0.4]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>oklch</code></th>
    <td><code>oklch(44% 0.16 303.4deg)</code></td>
    <td>
      lightness <span class="fade">[0%, 100%]</span>;
      chroma <span class="fade">[0, 0.4]</span>;
      hue <span class="fade">[0deg, 360deg]</span>
    </td>
  </tr>
</table>

标记为 * 的空间是 [传统颜色空间]。

[传统颜色空间]: #legacy-color-spaces

## 缺失的通道

CSS 和 Sass 中的颜色可以有“缺失的通道”，它们被写成 `none` 并表示一个其值未知或不影响颜色渲染方式的通道。例如，你可能写 `hsl(none 0% 50%)`，因为如果饱和度为 `0%`，色相并不重要。在大多数情况下，缺失的通道被视为 0 值，但它们偶尔会出现：

* 如果你将颜色混合在一起，无论是作为 CSS 插值的一部分（如动画）还是使用 Sass 的 [`color.mix()`] 函数，缺失的通道总是会采用另一个颜色的该通道的值（如果可能的话）。

  [`color.mix()`]: /documentation/modules/color#mix

* 如果你将具有缺失通道的颜色转换为具有类似通道的另一个空间，转换完成后该通道将被设置为 `none`。

虽然 [`color.channel()`] 将为缺失的通道返回 0，但你可以使用 [`color.is-missing()`] 来检查它们。

[`color.channel()`]: /documentation/modules/color#channel
[`color.is-missing()`]: /documentation/modules/color#is-missing

{% codeExample 'missing-channels', false %}
  @use 'sass:color';

  $grey: hsl(none 0% 50%);

  @debug color.mix($grey, blue, $method: hsl); // hsl(240, 50%, 50%)
  @debug color.to-space($grey, lch); // lch(53.3889647411% 0 none)
  ===
  @use 'sass:color'

  $grey: hsl(none 0% 50%)

  @debug color.mix($grey, blue, $method: hsl)  // hsl(240, 50%, 50%)
  @debug color.to-space($grey, lch)  // lch(53.3889647411% 0 none)
{% endcodeExample %}

### 无能通道

在某些情况下，如果颜色通道的值不影响颜色在屏幕上的渲染方式，则该通道被认为是“无能的”。CSS 规范要求，当颜色被转换为新空间时，任何无能通道都应被替换为 `none`。Sass 在所有情况下都这样做，除了转换为传统空间，以确保转换为传统空间总是产生与旧版浏览器兼容的颜色。

有关无能通道的更多详细信息，请参阅 [`color.is-powerless()`]。

[`color.is-powerless()`]: /documentation/modules/color#is-powerless

## 传统颜色空间

历史上，CSS 和 Sass 只支持标准 RGB 色域，并且只支持 `rgb`, `hsl`, 和 `hwb` 函数来定义颜色。由于当时所有颜色都使用相同的色域，因此每个颜色函数都可以与任何颜色一起工作，无论其颜色空间。Sass 仍然保留了这种行为，但仅适用于旧函数以及这些三个“传统”颜色空间中的颜色。即使如此，明确指定你想要工作的 `$space` 仍然是一个好习惯。

Sass 在将传统颜色值转换为 CSS 时也会自由地在不同的传统颜色空间之间进行转换。这是安全的，因为它们都使用相同的底层颜色模型，这有助于确保 Sass 发出的颜色格式尽可能兼容。

## 颜色函数

Sass 支持许多有用的 [颜色函数]，可以根据现有颜色通过 [混合颜色] 或 [缩放其通道值] 创建新颜色。调用颜色函数时，颜色空间应始终写成未加引号的字符串以匹配 CSS，而通道名称应写成加引号的字符串，以便通道如 `"red"` 不会被解析为颜色值。

[混合颜色]: /documentation/modules/color#mix
[缩放其通道值]: /documentation