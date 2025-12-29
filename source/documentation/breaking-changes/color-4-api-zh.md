---
title: '重大变更: Color JS API'
introduction: >
  之前设计的某些JS颜色API假设所有颜色都是相互兼容的，现在Sass支持CSS Color 4的所有颜色空间后，这些假设不再成立。
---

随着对 [CSS Color 4] 支持的增加，[Sass的颜色函数] 的某些方面已被弃用，JS API 中用于操作颜色的某些部分也被弃用。

[Sass的颜色函数]: /documentation/breaking-changes/color-functions
[CSS Color 4]: https://developer.mozilla.org/en-US/blog/css-color-module-level-4/

### `color.change()` 现在需要 `space` 参数以支持跨空间更改

以前，[`color.change()` 方法] 只接受来自 RGB、HSL 或 HWB 空间的通道名称集。只要这些通道没有在不同空间中混合使用（例如同时更改 `red` 和 `hue`），Sass 就能推断出预期的空间。

[`color.change()` 方法]: /documentation/js-api/classes/SassColor/#change

在 Color 4 中，仅从通道名称无法明确区分颜色空间。许多空间具有不同范围的 `red`、`green` 和 `blue` 通道；许多空间具有产生非常不同色轮的 `hue` 通道。为了解决这种歧义，`color.change()` 现在需要一个 `space` 参数，该参数显式指定要进行变换的颜色空间名称：

```js
const color = new sass.SassColor({red: 0x66, green: 0x33, blue: 0x99});
color.change({hue: 270, space: "okclh"});
```

如果要更改的颜色不在 [传统颜色空间] 中或更改仅存在于非传统颜色空间中的通道（如色度），则指定颜色空间是强制性的。如果更改的通道存在于颜色本身的通道中，则始终是可选的，因此 `color.change({red: 0.8})` 始终指的是任何具有 `red`、`green` 和 `blue` 通道的颜色的原生红色通道。

[传统颜色空间]: /documentation/values/colors#legacy-color-spaces

为了向后兼容，如果更改的是传统颜色的传统通道，Sass 将仍然自动转换颜色。但是，此行为已弃用。为了安全起见，除非确定颜色已经在要更改通道的颜色空间中，否则应始终传递 `space` 参数。

### `null` 通道值

CSS Color 4 的一个主要变化是引入了 ["缺失" 通道] 的新概念。例如，`hsl(none 0% 40%)` 具有缺失的色调，通常被视为 0，但在颜色插值中不起作用，因此使用此颜色的渐变不会在中间出现幻象红色色调。在构建颜色时，Sass 将缺失值表示为 `null` 值。

["缺失" 通道]: https://developer.mozilla.org/en-US/docs/Web/CSS/color_value#missing_color_components

在添加对 CSS Color 4 的支持之前，Sass JS API 的 TypeScript 类型禁止在所有相关位置使用 `null`。然而，代码本身将 `null` 视为与 `undefined` 相同，我们不想破坏任何依赖此行为的普通 JavaScript 代码的兼容性。目前，`null` 值被视为 `undefined`，并在构建新的 [传统颜色] 或调用 `color.change()` 处理传统颜色时发出弃用警告。在这两种情况下，如果显式传递 `space` 参数，则将采用新行为，并且 `null` 将被视为缺失通道。

[传统颜色]: /documentation/values/colors#legacy-color-spaces

## 过渡期

{% compatibility 'dart: "1.79.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

首先，我们将为所有计划更改的这些 API 的使用发出弃用警告。在 Dart Sass 2.0.0 中，重大更改将全面生效，旧的行为将不再按预期工作。

{% render 'silencing_deprecations', jsonly: true %}