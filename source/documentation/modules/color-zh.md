---
title: sass:color
---

{% render 'doc_snippets/built-in-module-status' %}

{% capture color_adjust %}
  color.adjust($color,
    $red: null, $green: null, $blue: null,
    $hue: null, $saturation: null, $lightness: null,
    $whiteness: null, $blackness: null,
    $x: null, $y: null, $z: null,
    $chroma: null,
    $alpha: null,
    $space: null)
{% endcapture %}

{% function color_adjust, 'adjust-color(...)', 'returns:color' %}
  {% compatibility 'dart: "1.79.0"', 'libsass: false', 'ruby: false', 'feature: "$x, $y, $z, $chroma, and $space"' %}{% endcompatibility %}
  {% compatibility 'dart: "1.28.0"', 'libsass: false', 'ruby: false', 'feature: "$whiteness and $blackness"' %}{% endcompatibility %}

  增加或减少 `$color` 的一个或多个通道的固定数量。

  将传递给每个关键字参数的值加到颜色的相应通道，并返回调整后的颜色。默认情况下，这只能调整 `$color` 的空间中的通道，但可以通过 `$space` 传递不同的颜色空间以在该空间中调整通道。这始终返回与 `$color` 相同空间的颜色。

  {% headsUp %}
    由于历史原因，如果 `$color` 在一个 [旧颜色空间] 中，_任何_ 旧颜色空间通道都可以被调整。但是，在同一时间指定 RGB 通道 (`$red`, `$green`, 和/或 `$blue`) 和 HSL 通道 (`$hue`, `$saturation`, 和/或 `$lightness`)，或任一与 [HWB] 通道 (`$hue`, `$whiteness`, 和/或 `$blackness`) 一起指定都是错误的。

    [旧颜色空间]: /documentation/values/colors#legacy-color-spaces
    [HWB]: https://en.wikipedia.org/wiki/HWB_color_model

    即使如此，对于旧颜色也最好显式地传递 `$space`。
  {% endheadsUp %}

  所有通道参数必须是数字，并且必须是可以传递给颜色空间构造函数的相应通道的单位。如果现有通道值加上调整值超出通道的本机范围，则会被截断：

  * `rgb` 空间的红色、绿色和蓝色通道；
  * `lab`, `lch`, `oklab`, 和 `oklch` 空间的亮度通道；
  * `hsl`, `lch`, 和 `oklch` 空间的饱和度和色度通道的下限；
  * 以及所有空间的 alpha 通道。

  参见：

  * [`color.scale()`](#scale) 流动缩放颜色属性。
  * [`color.change()`](#change) 设置颜色属性。

  {% codeExample 'adjust-color', false %}
    @use 'sass:color';

    @debug color.adjust(#6b717f, $red: 15); // #7a717f
    @debug color.adjust(lab(40% 30 40), $lightness: 10%, $a: -20); // lab(50% 10 40)
    @debug color.adjust(#d2e1dd, $hue: 45deg, $space: oklch);
    // rgb(209.7987626149, 223.8632000471, 229.3988769575)
    ===
    @use 'sass:color'

    @debug color.adjust(#6b717f, $red: 15)  // #7a717f
    @debug color.adjust(lab(40% 30 40), $lightness: 10%, $a: -20)  // lab(50% 10 40)
    @debug color.adjust(#d2e1dd, $hue: 45deg, $space: oklch)
    // rgb(209.7987626149, 223.8632000471, 229.3988769575)
  {% endcodeExample %}
{% endfunction %}

{% capture color_change %}
  color.change($color,
    $red: null, $green: null, $blue: null,
    $hue: null, $saturation: null, $lightness: null,
    $whiteness: null, $blackness: null,
    $x: null, $y: null, $z: null,
    $chroma: null,
    $alpha: null,
    $space: null)
{% endcapture %}

{% function color_change, 'change-color(...)', 'returns:color' %}
  {% compatibility 'dart: "1.79.0"', 'libsass: false', 'ruby: false', 'feature: "$x, $y, $z, $chroma, and $space"' %}{% endcompatibility %}
  {% compatibility 'dart: "1.28.0"', 'libsass: false', 'ruby: false', 'feature: "$whiteness and $blackness"' %}{% endcompatibility %}

  将颜色的一个或多个通道设置为新值。

  使用传递给每个关键字参数的值代替相应的颜色通道，并返回更改后的颜色。默认情况下，这只能更改 `$color` 的空间中的通道，但可以通过 `$space` 传递不同的颜色空间以在该空间中调整通道。这始终返回与 `$color` 相同空间的颜色。

  {% headsUp %}

    由于历史原因，如果 `$color` 在一个 [旧颜色空间] 中，_任何_ 旧颜色空间通道都可以被更改。但是，在同一时间指定 RGB 通道 (`$red`, `$green`, 和/或 `$blue`) 和 HSL 通道 (`$hue`, `$saturation`, 和/或 `$lightness`)，或任一与 [HWB] 通道 (`$hue`, `$whiteness`, 和/或 `$blackness`) 一起指定都是错误的。

    [旧颜色空间]: /documentation/values/colors#legacy-color-spaces
    [HWB]: https://en.wikipedia.org/wiki/HWB_color_model

    即使如此，对于旧颜色也最好显式地传递 `$space`。
  {% endheadsUp %}

  所有通道参数必须是数字，并且必须是可以传递给颜色空间构造函数的相应通道的单位。`color.change()` 从不截断通道。

  参见：

  * [`color.scale()`](#scale) 流动缩放颜色属性。
  * [`color.adjust()`](#adjust) 通过固定数量调整颜色属性。

  {% codeExample 'color-change', false %}
    @use 'sass:color';

    @debug color.change(#6b717f, $red: 100); // #64717f
    @debug color.change(color(srgb 0 0.2 0.4), $red: 0.8, $blue: 0.1);
    // color(srgb 0.8 0.2 0.1)
    @debug color.change(#998099, $lightness: 30%, $space: oklch);
    // rgb(58.0719961509, 37.2631531594, 58.4201613409)
    ===
    @use 'sass:color'

    @debug color.change(#6b717f, $red: 100)  // #64717f
    @debug color.change(color(srgb 0 0.2 0.4), $red: 0.8, $blue: 0.1)
    // color(srgb 0.8 0.2 0.1)
    @debug color.change(#998099, $lightness: 30%, $space: oklch)
    // rgb(58.0719961509, 37.2631531594, 58.4201613409)
  {% endcodeExample %}
{% endfunction %}

{% function 'color.channel($color, $channel, $space: null)', 'returns:number' %}
  {% compatibility 'dart: "1.79.0"', 'libsass: false', 'ruby: false', 'feature: "$space"' %}{% endcompatibility %}

  返回 `$color` 在 `$space` 中的 `$channel` 的值，`$space` 默认为 `$color` 的空间。`$channel` 必须是一个带引号的字符串，`$space` 必须是一个不带引号的字符串。

  对于 `hsl`, `hwb`, `lch`, 和 `oklch` 空间的 `hue` 通道，这返回一个带有 `deg` 单位的数字。对于 `hsl`, `hwb`, `lab`, `lch`, `oklab`, 和 `oklch` 空间的 `saturation`, `lightness`, `whiteness`, 和 `blackness` 通道，这返回一个带有 `%` 单位的数字。对于所有其他通道，它返回一个无单位的数字。

  如果 `$color` 中缺少 `$channel`，这将返回 `0`（可能带有适当的单位）。您可以使用 [`color.is-missing()`] 显式检查缺少的通道。

  [`color.is-missing()`]: #is-missing

  {% codeExample 'color-channel', false %}
    @use 'sass:color';

    @debug color.channel(hsl(80deg 30% 50%), "hue"); // 80deg
    @debug color.channel(hsl(80deg 30% 50%), "hue", $space: oklch); // 124.279238779deg
    @debug color.channel(hsl(80deg 30% 50%), "red", $space: rgb); // 140.25
    ===
    @use 'sass:color'

    @debug color.channel(hsl(80deg 30% 50%), "hue")  // 80deg
    @debug color.channel(hsl(80deg 30% 50%), "hue", $space: oklch)  // 124.279238779deg
    @debug color.channel(hsl(80deg 30% 50%), "red", $space: rgb)  // 140.25
  {% endcodeExample %}
{% endfunction %}

{% function 'color.complement($color, $space: null)', 'complement($color, $space: null)', 'returns:color' %}
  {% compatibility 'dart: "1.79.0"', 'libsass: false', 'ruby: false', 'feature: "$space"' %}{% endcompatibility %}

  返回 `$color` 在 `$space` 中的 [补色]。

  [补色]: https://en.wikipedia.org/wiki/Complementary_colors

  这将 `$color` 的色调在 `$space` 中旋转 `180deg`。这意味着 `$space` 必须是一个极坐标颜色空间：`hsl`, `hwb`, `lch`, 或 `oklch`。它始终返回与 `$color` 相同空间的颜色。

  {% headsUp %}
    由于历史原因，如果 `$color` 在一个 [旧颜色空间] 中，`$space` 是可选的。在这种情况下，`$space` 默认为 `hsl`。无论如何，最好总是显式地传递 `$space`。

    [旧颜色空间]: /documentation/values/colors#legacy-color-spaces
  {% endheadsUp %}

  {% codeExample 'color-complement', false %}
    @use 'sass:color';

    // HSL 色调 222deg 变为 42deg。
    @debug color.complement(#6b717f); // #7f796b

    // Oklch 色调 267.1262408996deg 变为 87.1262408996deg
    @debug color.complement(#6b717f, oklch);
    // rgb(118.8110604298, 112.5123650034, 98.1616586336)

    // 色调 70deg 变为 250deg。
    @debug color.complement(oklch(50% 0.12 70deg), oklch); // oklch(50% 0.12 250deg)
    ===
    @use 'sass:color'

    // HSL 色调 222deg 变为 42deg。
    @debug color.complement(#6b717f)  // #7f796b

    // Oklch 色调 267.1262408996deg 变为 87.1262408996deg
    @debug color.complement(#6b717f, oklch) 
    // rgb(118.8110604298, 112.5123650034, 98.1616586336)

    // 色调 70deg 变为 250deg。
    @debug color.complement(oklch(50% 0.12 70deg), oklch)  // oklch(50% 0.12 250deg)
  {% endcodeExample %}
{% endfunction %}

{% function 'color.grayscale($color)', 'grayscale($color)', 'returns:color' %}
  返回一个与 `$color` 具有相同亮度的灰色颜色。

  如果 `$color` 在一个 [旧颜色空间] 中，这将 HSL 饱和度设置为 0%。否则，它将 Oklch 色度设置为 0%。

  [旧颜色空间]: /documentation/values/colors#legacy-color-spaces

  {% codeExample 'color-grayscale', false %}
    @use 'sass:color';

    @debug color.grayscale(#6b717f); // #757575
    @debug color.grayscale(color(srgb 0.4 0.2 0.6)); // color(srgb 0.3233585271 0.3233585411 0.3233585792)
    @debug color.grayscale(oklch(50% 80% 270deg)); // oklch(50% 0% 270deg)
    ===
    @use 'sass:color'

    @debug color.grayscale(#6b717f)  // #757575
    @debug color.grayscale(color(srgb 0.4 0.2 0.6))  // color(srgb 0.3233585271 0.3233585411 0.3233585792)
    @debug color.grayscale(oklch(50% 80% 270deg))  // oklch(50% 0% 270deg)
  {% endcodeExample %}
{% endfunction %}

{% function 'color.ie-hex-str($color)', 'ie-hex-str($color)', 'returns:unquoted string' %}
  返回一个未带引号的字符串，该字符串表示 `$color` 以 Internet Explorer 的 [`-ms-filter`] 属性所期望的 `#AARRGGBB` 格式。

  [`-ms-filter`]: https://learn.microsoft.com/en-us/previous-versions/ms530752(v=vs.85)

  如果 `$color` 不在 `rgb` 颜色空间中，它将被转换为 `rgb` 并进行色域映射（如果必要）。具体的色域映射算法可能会随着 Sass 版本的更新而变化；目前使用的是 [`local-minde`]。

  [`local-minde`]: #to-gamut

  {% codeExample 'color-ie-hex-str', false %}
    @use 'sass:color';

    @debug color.ie-hex-str(#b37399); // #FFB37399
    @debug color.ie-hex-str(rgba(242, 236, 228, 0.6)); // #99F2ECE4
    @debug color.ie-hex-str(oklch(70% 10% 120deg)); // #FF9BA287
    ===
    @use 'sass:color'

    @debug color.ie-hex-str(#b37399)  // #FFB37399
    @debug color.ie-hex-str(rgba(242, 236, 228, 0.6))  // #99F2ECE4
    @debug color.ie-hex-str(oklch(70% 10% 120deg))  // #FF9BA287
  {% endcodeExample %}
{% endfunction %}

{% function 'color.invert($color, $weight: 100%, $space: null)', 'invert($color, $weight: 100%, $space: null)', 'returns:color' %}
  {% compatibility 'dart: "1.79.0"', 'libsass: false', 'ruby: false', 'feature: "$space"' %}{% endcompatibility %}

 

@debug color.same(hsl(none 50% 50%), hsl(0deg 50% 50%)); // true
===
@use 'sass:color'

@debug color.same(#036, #036)  // true
@debug color.same(#036, #037)  // false
@debug color.same(#036, color.to-space(#036, oklch))  // true
@debug color.same(hsl(none 50% 50%), hsl(0deg 50% 50%))  // true
  {% endcodeExample %}
{% endfunction %}

{% capture color_scale %}
  color.scale($color,
    $red: null, $green: null, $blue: null,
    $saturation: null, $lightness: null,
    $whiteness: null, $blackness: null,
    $x: null, $y: null, $z: null,
    $chroma: null,
    $alpha: null,
    $space: null)
{% endcapture %}

{% function color_scale, 'scale-color(...)', 'returns:color' %}
  {% compatibility 'dart: "1.79.0"', 'libsass: false', 'ruby: false', 'feature: "$x, $y, $z, $chroma, and $space"' %}{% endcompatibility %}
  {% compatibility 'dart: "1.28.0"', 'libsass: false', 'ruby: false', 'feature: "$whiteness and $blackness"' %}{% endcompatibility %}

  流畅地缩放 `$color` 的一个或多个属性。

  每个关键字参数必须是一个介于 `-100%` 和 `100%`（包括）之间的数字。
  这表示相应的属性应该从其原始位置向最大值（如果参数为正）或最小值（如果参数为负）移动多少。例如，`$lightness: 50%` 将使所有颜色的亮度接近最大亮度 50%，但不会使它们完全变白。默认情况下，这只能缩放 `$color` 的空间中的颜色，但可以通过 `$space` 传递不同的颜色空间以在其中缩放通道。这始终返回与 `$color` 相同空间的颜色。

  {% headsUp %}
    历史原因，如果 `$color` 处于 [旧颜色空间]，_任何_ 旧颜色空间通道都可以被缩放。但是，同时指定 RGB 通道（`$red`，`$green` 和/或 `$blue`）和 HSL 通道（`$saturation` 和/或 `$lightness`），或任一者与 [HWB] 通道（`$hue`，`$whiteness` 和/或 `$blackness`）一起指定是错误的。

    [旧颜色空间]: /documentation/values/colors#legacy-color-spaces
    [HWB]: https://en.wikipedia.org/wiki/HWB_color_model

    即使如此，为旧颜色显式传递 `$space` 也是一个好主意。
  {% endheadsUp %}

  [HWB]: https://en.wikipedia.org/wiki/HWB_color_model

  参见：

  * [`color.adjust()`](#adjust) 通过固定数量更改颜色的属性。
  * [`color.change()`](#change) 设置颜色的属性。

  {% codeExample 'color-scale', false %}
    @use 'sass:color';

    @debug color.scale(#6b717f, $red: 15%); // rgb(129.2, 113, 127)
    @debug color.scale(#d2e1dd, $lightness: -10%, $space: oklch);
    // rgb(181.2580722731, 195.8949200496, 192.0059024063)
    @debug color.scale(oklch(80% 20% 120deg), $chroma: 50%, $alpha: -40%);
    // oklch(80% 0.24 120deg / 0.6)
    ===
    @use 'sass:color'

    @debug color.scale(#6b717f, $red: 15%)  // rgb(129.2, 113, 127)
    @debug color.scale(#d2e1dd, $lightness: -10%, $space: oklch)
    // rgb(181.2580722731, 195.8949200496, 192.0059024063)
    @debug color.scale(oklch(80% 20% 120deg), $chroma: 50%, $alpha: -40%)
    // oklch(80% 0.24 120deg / 0.6)
  {% endcodeExample %}
{% endfunction %}

{% function 'color.space($color)', 'returns:unquoted string' %}
  {% compatibility 'dart: "1.79.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回 `$color` 的空间名称作为未引用的字符串。

  {% codeExample 'color-space', false %}
    @use 'sass:color';

    @debug color.space(#036); // rgb
    @debug color.space(hsl(120deg 40% 50%)); // hsl
    @debug color.space(color(xyz-d65 0.1 0.2 0.3)); // xyz
    ===
    @use 'sass:color'

    @debug color.space(#036)  // rgb
    @debug color.space(hsl(120deg 40% 50%))  // hsl
    @debug color.space(color(xyz-d65 0.1 0.2 0.3))  // xyz
  {% endcodeExample %}
{% endfunction %}

{% function 'color.to-gamut($color, $space: null, $method: null)', 'returns:color' %}
  {% compatibility 'dart: "1.79.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回一个与 `$color` 视觉上相似的颜色，该颜色在 `$space` 的色域内，`$space` 默认为 `$color` 的空间。如果 `$color` 已经在 `$space` 的色域内，则原样返回。这始终返回 `$color` 的原始空间的颜色。`$space` 必须是一个未引用的字符串。

  `$method` 表示 Sass 应该如何选择一个“相似”的颜色：

  * `local-minde`: 这是 CSS Colors 4 规范目前推荐的方法。它在颜色的 Oklch 色度空间中进行二分查找，直到找到一个其裁剪到色域内的值与减小色度的变体尽可能接近的颜色。

  * `clip`: 这只是简单地将所有通道裁剪到 `$space` 的色域内，如果它们超出色域范围，则将其设置为色域的最小或最大值。

  {% headsUp %}
    CSS 工作组和浏览器供应商仍在积极讨论推荐的色域映射算法的替代选项。在他们达成推荐之前，`color.to-gamut()` 中的 `$method` 参数是强制的，以便我们最终可以使其默认值与 CSS 的默认值相同。
  {% endheadsUp %}

  {% codeExample 'color-to-gamut', false %}
    @use 'sass:color';

    @debug color.to-gamut(#036, $method: local-minde); // #036
    @debug color.to-gamut(oklch(60% 70% 20deg), $space: rgb, $method: local-minde);
    // oklch(61.2058838235% 0.2466052584 22.0773325274deg)
    @debug color.to-gamut(oklch(60% 70% 20deg), $space: rgb, $method: clip);
    // oklch(62.5026609544% 0.2528579741 24.1000466758deg)
    ===
    @use 'sass:color'

    @debug color.to-gamut(#036, $method: local-minde)  // #036
    @debug color.to-gamut(oklch(60% 70% 20deg), $space: rgb, $method: local-minde)
    // oklch(61.2058838235% 0.2466052584 22.0773325274deg)
    @debug color.to-gamut(oklch(60% 70% 20deg), $space: rgb, $method: clip)
    // oklch(62.5026609544% 0.2528579741 24.1000466758deg)
  {% endcodeExample %}
{% endfunction %}

{% function 'color.to-space($color, $space)', 'returns:color' %}
  {% compatibility 'dart: "1.79.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  将 `$color` 转换为给定的 `$space`，`$space` 必须是一个未引用的字符串。

  如果 `$color` 的原始空间的色域比 `$space` 的色域宽，这可能会返回一个超出 `$space` 色域的颜色。您可以使用 [`color.to-gamut()`] 将其转换为相似的在色域内的颜色。

  [`color.to-gamut()`]: #to-gamut

  这可能会产生具有 [缺失通道] 的颜色，要么是 `$color` 具有 [类似通道] 但缺失，要么是通道在目标空间中 [无效]。为了确保转换为旧颜色空间总是产生与旧浏览器兼容的颜色，如果 `$space` 是旧的，这将永远不会返回新的缺失通道。

  [缺失通道]: /documentation/values/colors#missing-channels
  [类似通道]: https://www.w3.org/TR/css-color-4/#analogous-components
  [无效]: /documentation/values/colors#powerless-channels
  
  {% funFact %}
    这是唯一一个返回与传入空间不同的颜色的 Sass 函数。
  {% endfunFact %}

  {% codeExample 'color-to-space', false %}
    @use 'sass:color';

    @debug color.to-space(#036, display-p3); // lch(20.7457453073% 35.0389733355 273.0881809283deg)
    @debug color.to-space(oklab(44% 0.09 -0.13)); // rgb(103.1328911972, 50.9728091281, 150.8382311692)
    @debug color.to-space(xyz(0.8 0.1 0.1)); // color(a98-rgb 1.2177586808 -0.7828263424 0.3516847577)
    @debug color.to-space(grey, lch); // lch(53.5850134522% 0 none)
    @debug color.to-space(lch(none 10% 30deg), oklch); // oklch(none 0.3782382429 11.1889160032deg)
    ===
    @use 'sass:color'

    @debug color.to-space(#036, display-p3)  // lch(20.7457453073% 35.0389733355 273.0881809283deg)
    @debug color.to-space(oklab(44% 0.09 -0.13))  // rgb(103.1328911972, 50.9728091281, 150.8382311692)
    @debug color.to-space(xyz(0.8 0.1 0.1))  // color(a98-rgb 1.2177586808 -0.7828263424 0.3516847577)
    @debug color.to-space(grey, lch)  // lch(53.5850134522% 0 none)
    @debug color.to-space(lch(none 10% 30deg), oklch)  // oklch(none 0.3782382429 11.1889160032deg)
  {% endcodeExample %}
{% endfunction %}

## 废弃的函数

{% function 'adjust-hue($color, $degrees)', 'returns:color' %}
  增加或减少 `$color` 的 HSL 色相。

  `$hue` 必须是一个介于 `-360deg` 和 `360deg`（包括）之间的数字，用于添加到 `$color` 的色相。它可以是 [无单位] 或具有任何角度单位。`$color` 必须处于 [旧颜色空间]。

  [无单位]: /documentation/values/numbers#units
  [旧颜色空间]: /documentation/values/colors#legacy-color-spaces

  参见 [`color.adjust()`](#adjust)，它可以调整颜色的任何属性。

  {% headsUp %}
    因为 `adjust-hue()` 与 [`color.adjust()`](#adjust) 冗余，它没有直接包含在新的模块系统中。与其使用 `adjust-hue($color, $amount)`，您可以编写 [`color.adjust($color, $hue: $amount, $space: hsl)`](#adjust)。
  {% endheadsUp %}

  {% codeExample 'adjust-hue' %}
    // 色相 222deg 变为 282deg。
    @debug adjust-hue(#6b717f, 60deg); // #796b7f

    // 色相 164deg 变为 104deg。
    @debug adjust-hue(#d2e1dd, -60deg); // #d6e1d2

    // 色相 210deg 变为 255deg。
    @debug adjust-hue(#036, 45); // #1a0066
    ===
    // 色相 222deg 变为 282deg。
    @debug adjust-hue(#6b717f, 60deg)  // #796b7f

    // 色相 164deg 变为 104deg。
    @debug adjust-hue(#d2e1dd, -60deg)  // #d6e1d2

    // 色相 210deg 变为 255deg。
    @debug adjust-hue(#036, 45)  // #1a0066
  {% endcodeExample %}
{% endfunction %}

{% function 'color.alpha($color)', 'alpha($color)', 'opacity($color)', 'returns:number' %}
  返回 `$color` 的 alpha 通道作为一个介于 0 和 1 之间的数字。
  
  `$color` 必须处于 [旧颜色空间]。

  [旧颜色空间]: /documentation/values/colors#legacy-color-spaces

  作为特殊情况，它支持 Internet Explorer 语法 `alpha(opacity=20)`，对于该语法它返回一个 [未引用的字符串]。

  [未引用的字符串]: /documentation/values/strings#unquoted

  {% headsUp %}
    因为 `color.alpha()` 与 [`color.channel()`](#channel) 冗余，不再推荐使用。与其使用 `color.alpha($color)`，您可以编写 [`color.channel($color, "alpha")`](#channel)。
  {% endheadsUp %}

  {% codeExample 'color-alpha' %}
    @use 'sass:color';

    @debug color.alpha(#e1d7d2); // 1
    @debug color.opacity(rgb(210, 225, 221, 0.4)); // 0.4
    @debug alpha(opacity=20); // alpha(opacity=20)
    ===
    @use 'sass:color'

    @debug color.alpha(#e1d7d2)  // 1
    @debug color.opacity(rgb(210, 225, 221, 0.4))  // 0.4
    @debug alpha(opacity=20)  // alpha(opacity=20)
  {% endcodeExample %}
{% endfunction %}

{% function 'color.blackness($color)', 'blackness($color)', 'returns:number' %}
  {% compatibility 'dart: "1.28.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回 `$color` 的 [HWB] 黑度作为一个介于 `0%` 和 `100%` 之间的数字。

  [HWB]: https://en.wikipedia.org/wiki/HWB_color

[legacy color space]: /documentation/values/colors#legacy-color-spaces

  `$amount` 必须是一个介于 `0%` 和 `100%`（包括）之间的数字。通过该数值增加 `$color` 的 HSL 明度。

  {% headsUp %}
    `lighten()` 函数通过固定数值增加明度，这通常不是预期的效果。要使颜色比原来亮某个百分比，应使用 [`scale()`](#scale) 代替。

    由于 `lighten()` 通常不是使颜色变亮的最佳方法，因此它没有直接包含在新的模块系统中。但是，如果您必须保留现有行为，`lighten($color, $amount)` 可以写成 [`color.adjust($color, $lightness: $amount, $space: hsl)`](#adjust)。

    {% codeExample 'color-lighten' %}
      @use 'sass:color';

      // #e1d7d2 的明度为 85%，因此当 lighten() 增加 30% 时，它只返回白色。
      @debug lighten(#e1d7d2, 30%); // white

      // 而 scale() 则使其比原来亮 30%。
      @debug color.scale(#e1d7d2, $lightness: 30%); // #eae3e0
      ===
      @use 'sass:color'

      // #e1d7d2 的明度为 85%，因此当 lighten() 增加 30% 时，它只返回白色。
      @debug lighten(#e1d7d2, 30%)  // white

      // 而 scale() 则使其比原来亮 30%。
      @debug color.scale(#e1d7d2, $lightness: 30%)  // #eae3e0
    {% endcodeExample %}
  {% endheadsUp %}

  {% codeExample 'color-lighten-2' %}
    // 明度 46% 变为 66%。
    @debug lighten(#6b717f, 20%); // #a1a5af

    // 明度 20% 变为 80%。
    @debug lighten(#036, 60%); // #99ccff

    // 明度 85% 变为 100%。
    @debug lighten(#e1d7d2, 30%); // white
    ===
    // 明度 46% 变为 66%。
    @debug lighten(#6b717f, 20%)  // #a1a5af

    // 明度 20% 变为 80%。
    @debug lighten(#036, 60%)  // #99ccff

    // 明度 85% 变为 100%。
    @debug lighten(#e1d7d2, 30%)  // white
  {% endcodeExample %}
{% endfunction %}

{% function 'color.lightness($color)', 'lightness($color)', 'returns:number' %}
  返回 `$color` 的 HSL 明度，范围为 `0%` 到 `100%` 之间的数字。

  `$color` 必须在 [legacy color space] 中。

  [legacy color space]: /documentation/values/colors#legacy-color-spaces

  {% headsUp %}
    由于 `color.lightness()` 与 [`color.channel()`](#channel) 重复，因此不再推荐使用。与其使用 `color.lightness($color)`，可以写成 [`color.channel($color, "lightness")`](#channel)。
  {% endheadsUp %}

  {% codeExample 'color-lightness' %}
    @use 'sass:color';

    @debug color.lightness(#e1d7d2); // 85.2941176471%
    @debug color.lightness(#f2ece4); // 92.1568627451%
    @debug color.lightness(#dadbdf); // 86.4705882353%
    ===
    @use 'sass:color'

    @debug color.lightness(#e1d7d2)  // 85.2941176471%
    @debug color.lightness(#f2ece4)  // 92.1568627451%
    @debug color.lightness(#dadbdf)  // 86.4705882353%
  {% endcodeExample %}
{% endfunction %}

{% function 'opacify($color, $amount)', 'fade-in($color, $amount)', 'returns:color' %}
  使 `$color` 更不透明。

  `$color` 必须在 [legacy color space] 中。

  [legacy color space]: /documentation/values/colors#legacy-color-spaces

  `$amount` 必须是一个介于 `0` 和 `1`（包括）之间的数字。通过该数值增加 `$color` 的 alpha 通道。

  {% headsUp %}
    `opacify()` 函数通过固定数值增加 alpha 通道，这通常不是预期的效果。要使颜色比原来更不透明某个百分比，应使用 [`scale()`](#scale) 代替。

    由于 `opacify()` 通常不是使颜色更不透明的最佳方法，因此它没有直接包含在新的模块系统中。但是，如果您必须保留现有行为，`opacify($color, $amount)` 可以写成 [`color.adjust($color, $alpha: -$amount)`](#adjust)。

    {% codeExample 'color-opacify' %}
      @use 'sass:color';

      // rgba(#036, 0.7) 的 alpha 为 0.7，因此当 opacify() 增加 0.3 时，它返回一个完全不透明的颜色。
      @debug opacify(rgba(#036, 0.7), 0.3); // #036

      // 而 scale() 则使其比原来更不透明 30%。
      @debug color.scale(rgba(#036, 0.7), $alpha: 30%); // rgba(0, 51, 102, 0.79)
      ===
      @use 'sass:color'

      // rgba(#036, 0.7) 的 alpha 为 0.7，因此当 opacify() 增加 0.3 时，它返回一个完全不透明的颜色。
      @debug opacify(rgba(#036, 0.7), 0.3)  // #036

      // 而 scale() 则使其比原来更不透明 30%。
      @debug color.scale(rgba(#036, 0.7), $alpha: 30%)  // rgba(0, 51, 102, 0.79)
    {% endcodeExample %}
  {% endheadsUp %}

  {% codeExample 'color-opacify-2' %}
    @debug opacify(rgba(#6b717f, 0.5), 0.2); // rgba(107, 113, 127, 0.7)
    @debug fade-in(rgba(#e1d7d2, 0.5), 0.4); // rgba(225, 215, 210, 0.9)
    @debug opacify(rgba(#036, 0.7), 0.3); // #036
    ===
    @debug opacify(rgba(#6b717f, 0.5), 0.2)  // rgba(107, 113, 127, 0.7)
    @debug fade-in(rgba(#e1d7d2, 0.5), 0.4)  // rgba(225, 215, 210, 0.9)
    @debug opacify(rgba(#036, 0.7), 0.3)  // #036
  {% endcodeExample %}
{% endfunction %}

{% function 'color.red($color)', 'red($color)', 'returns:number' %}
  返回 `$color` 的红色通道，范围为 0 到 255 之间的数字。

  `$color` 必须在 [legacy color space] 中。

  [legacy color space]: /documentation/values/colors#legacy-color-spaces

  {% headsUp %}
    由于 `color.red()` 与 [`color.channel()`](#channel) 重复，因此不再推荐使用。与其使用 `color.red($color)`，可以写成 [`color.channel($color, "red")`](#channel)。
  {% endheadsUp %}

  {% codeExample 'color-red' %}
    @use 'sass:color';

    @debug color.red(#e1d7d2); // 225
    @debug color.red(white); // 255
    @debug color.red(black); // 0
    ===
    @use 'sass:color'

    @debug color.red(#e1d7d2)  // 225
    @debug color.red(white)  // 255
    @debug color.red(black)  // 0
  {% endcodeExample %}
{% endfunction %}

{% function 'saturate($color, $amount)', 'returns:color' %}
  使 `$color` 更饱和。

  `$color` 必须在 [legacy color space] 中。

  [legacy color space]: /documentation/values/colors#legacy-color-spaces

  `$amount` 必须是一个介于 `0%` 和 `100%`（包括）之间的数字。通过该数值增加 `$color` 的 HSL 饱和度。

  {% headsUp %}
    `saturate()` 函数通过固定数值增加饱和度，这通常不是预期的效果。要使颜色比原来更饱和某个百分比，应使用 [`scale()`](#scale) 代替。

    由于 `saturate()` 通常不是使颜色更饱和的最佳方法，因此它没有直接包含在新的模块系统中。但是，如果您必须保留现有行为，`saturate($color, $amount)` 可以写成 [`color.adjust($color, $saturation: $amount, $space: hsl)`](#adjust)。

    {% codeExample 'color-saturate' %}
      @use 'sass:color';

      // #0e4982 的饱和度为 80%，因此当 saturate() 增加 30% 时，它只变为完全饱和。
      @debug saturate(#0e4982, 30%); // #004990

      // 而 scale() 则使其比原来更饱和 30%。
      @debug color.scale(#0e4982, $saturation: 30%); // #0a4986
      ===
      @use 'sass:color'

      // #0e4982 的饱和度为 80%，因此当 saturate() 增加 30% 时，它只变为完全饱和。
      @debug saturate(#0e4982, 30%)  // #004990

      // 而 scale() 则使其比原来更饱和 30%。
      @debug color.scale(#0e4982, $saturation: 30%)  // #0a4986
    {% endcodeExample %}
  {% endheadsUp %}

  {% codeExample 'color-saturate-2' %}
    // 饱和度 50% 变为 70%。
    @debug saturate(#c69, 20%); // #e05299

    // 饱和度 35% 变为 85%。
    @debug desaturate(#f2ece4, 50%); // #ebebeb

    // 饱和度 80% 变为 100%。
    @debug saturate(#0e4982, 30%)  // #004990
    ===
    // 饱和度 50% 变为 70%。
    @debug saturate(#c69, 20%); // #e05299

    // 饱和度 35% 变为 85%。
    @debug desaturate(#f2ece4, 50%); // #ebebeb

    // 饱和度 80% 变为 100%。
    @debug saturate(#0e4982, 30%)  // #004990
  {% endcodeExample %}
{% endfunction %}

{% function 'color.saturation($color)', 'saturation($color)', 'returns:number' %}
  返回 `$color` 的 HSL 饱和度，范围为 `0%` 到 `100%` 之间的数字。

  `$color` 必须在 [legacy color space] 中。

  [legacy color space]: /documentation/values/colors#legacy-color-spaces

  {% headsUp %}
    由于 `color.saturation()` 与 [`color.channel()`](#channel) 重复，因此不再推荐使用。与其使用 `color.saturation($color)`，可以写成 [`color.channel($color, "saturation")`](#channel)。
  {% endheadsUp %}

  {% codeExample 'color-saturation' %}
    @use 'sass:color';

    @debug color.saturation(#e1d7d2); // 20%
    @debug color.saturation(#f2ece4); // 30%
    @debug color.saturation(#dadbdf); // 7.2463768116%
    ===
    @use 'sass:color'

    @debug color.saturation(#e1d7d2)  // 20%
    @debug color.saturation(#f2ece4)  // 30%
    @debug color.saturation(#dadbdf)  // 7.2463768116%
  {% endcodeExample %}
{% endfunction %}

{% function 'transparentize($color, $amount)', 'fade-out($color, $amount)', 'returns:color' %}
  使 `$color` 更透明。

  `$color` 必须在 [legacy color space] 中。

  [legacy color space]: /documentation/values/colors#legacy-color-spaces

  `$amount` 必须是一个介于 `0` 和 `1`（包括）之间的数字。通过该数值减少 `$color` 的 alpha 通道。

  {% headsUp %}
    `transparentize()` 函数通过固定数值减少 alpha 通道，这通常不是预期的效果。要使颜色比原来更透明某个百分比，应使用 [`color.scale()`](#scale) 代替。

    由于 `transparentize()` 通常不是使颜色更透明的最佳方法，因此它没有直接包含在新的模块系统中。但是，如果您必须保留现有行为，`transparentize($color, $amount)` 可以写成 [`color.adjust($color, $alpha: -$amount, $space: hsl)`](#adjust)。

    {% codeExample 'transparentize' %}
      @use 'sass:color';

      // rgba(#036, 0.3) 的 alpha 为 0.3，因此当 transparentize() 减少 0.3 时，它返回一个完全透明的颜色。
      @debug transparentize(rgba(#036, 0.3), 0.3); // rgba(0, 51, 102, 0)

      // 而 scale() 则使其比原来更透明 30%。
      @debug color.scale(rgba(#036, 0.3), $alpha: -30%); // rgba(0, 51, 102, 0.21)
      ===
      @use 'sass:color'

      // rgba(#036, 0.3) 的 alpha 为 0.3，因此当 transparentize() 减少 0.3 时，它返回一个完全透明的颜色。
      @debug transparentize(rgba(#036, 0.3), 0.3)  // rgba(0, 51, 102, 0)

      // 而 scale() 则使其比原来更透明 30%。
      @debug color.scale(rgba(#036, 0.3), $alpha: -30%)  // rgba(0, 51, 102, 0.21)
    {% endcodeExample %}
  {% endheadsUp %}

  {% codeExample 'transparentize-2' %}
    @debug transparentize(rgba(#6b717f, 0.5), 0.2);  // rgba(107, 113, 127, 0.3)
    @debug fade-out(rgba(#e1d7d2, 0.5), 0.4);  // rgba(225, 215, 210, 0.1)
    @debug transparentize(rgba(#036, 0.3), 0.3);  // rgba(0, 51, 102, 0)
    ===
    @debug transparentize(rgba(#6b717f, 0.5), 0.2)  // rgba(107, 113, 127, 0.3)
    @debug fade-out(rgba(#e1d7d2, 0.5), 0.4)  // rgba(225, 215, 210, 0.1)
    @debug transparentize(rgba(#036, 0.3), 0.3)  // rgba(0, 51, 10