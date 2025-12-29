---
title: '重大变更：严格的函数单位'
introduction: >
  各种内置函数将对其允许的单位变得更加严格，并且会更一致地处理这些单位。这使得 Sass 更兼容 CSS 规范，并有助于更快地捕获错误。
---

## 色相

{% compatibility 'dart: "1.32.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

在指定颜色的色相时，CSS 允许任何 [角度单位][]（`deg`，`grad`，`rad` 或 `turn`）。它还允许一个无单位的数字，该数字被视为 `deg`。历史上，Sass 允许 *任何* 单位，并将其解释为 `deg`。这尤其有问题，因为这意味着有效的 CSS 表达式 `hsl(0.5turn, 100%, 50%)` 将被 Sass 允许，但会被完全错误地解释。

[角度单位]: https://drafts.csswg.org/css-values-4/#angles

为了解决此问题并使 Sass 符合 CSS 规范，我们将在多个阶段进行更改：

### 阶段 1

{% compatibility 'dart: "1.32.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

最初，如果将带有 `deg` 以外单位的数字作为色相传递给任何函数，Sass 会发出弃用警告。仍然允许传递无单位的数字。

### 阶段 2

{% compatibility 'dart: "1.52.1"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

接下来，我们将更改色相参数的角度单位处理方式以符合 CSS 规范。这意味着带有 `grad`，`rad` 或 `turn` 单位的数字将被转换为 `deg`：`0.5turn` 将被转换为 `180deg`，`100grad` 将被转换为 `90deg`，依此类推。

由于此更改对于保持 CSS 兼容性是必要的，根据 [Dart Sass 兼容性策略]，它仅通过次要版本更新进行。但是，它尽可能少地更改行为，以确保 Sass 根据 CSS 规范解释所有有效的 CSS。

[Dart Sass 兼容性策略]: https://github.com/sass/dart-sass#compatibility-policy

### 阶段 3

{% compatibility 'dart: false', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

最后，在 Dart Sass 2.0.0 中，如果颜色函数接收到非角度单位的色相参数，它们将抛出错误。仍然允许无单位的色相。

## 饱和度和亮度

在指定 HSL 颜色的饱和度和亮度时，CSS 仅允许 `%` 单位。甚至不允许无单位的数字（与色相不同）。历史上，Sass 允许 *任何* 单位，并将其解释为 `%`。你甚至可以写 `hsl(0, 100px, 50s)`，而 Sass 将返回颜色 `red`。

为了解决此问题并使 Sass 符合 CSS 规范，我们将在两个阶段进行更改：

### 阶段 1

{% compatibility 'dart: "1.32.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

目前，如果将带有无单位或 `%` 以外单位的数字作为亮度或饱和度传递给任何函数，Sass 会发出弃用警告。

### 阶段 2

{% compatibility 'dart: false', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

在 Dart Sass 2.0.0 中，如果颜色函数接收到无单位或非 `%` 单位的饱和度或亮度参数，它们将抛出错误。

## 透明度

在指定颜色的透明度值时，CSS（自 [颜色级别 4] 起）允许介于 0 和 1 之间的无单位值或介于 `0%` 和 `100%` 之间的 `%` 值。在大多数情况下，Sass 遵循此行为，但函数 `color.adjust()` 和 `color.change()` 历史上允许 *任何* 单位，并将其解释为无单位。你甚至可以写 `color.change(red, $alpha: 1%)`，而 Sass 将返回不透明的颜色 `red`。

[颜色级别 4]: https://www.w3.org/TR/css-color-4/#typedef-alpha-value

为了解决此问题并使 Sass 符合 CSS 规范，我们将在三个阶段进行更改：

### 阶段 1

{% compatibility 'dart: "1.56.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

目前，如果将带有任何单位的数字（包括 `%`）作为透明度值传递给 `color.change()` 或 `color.adjust()`，Sass 会发出弃用警告。

### 阶段 2

{% compatibility 'dart: false', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

接下来，我们将更改 `color.change()` 和 `color.adjust()` 的透明度参数的 `%` 单位处理方式。带有 `%` 单位的透明度将被除以 `100%`，转换为介于 0 和 1 之间的无单位数字。

由于此更改是一个修复了与其他 Sass 函数一致性的错误，它仅通过次要版本更新进行。它将在阶段 1 发布后至少三个月进行更改，以给用户时间调整代码并避免错误。

[Dart Sass 兼容性策略]: https://github.com/sass/dart-sass#compatibility-policy

### 阶段 3

{% compatibility 'dart: false', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

最后，在 Dart Sass 2.0.0 中，如果 `color.change()` 和 `color.adjust()` 接收到非 `%` 单位的透明度参数，它们将抛出错误。仍然允许无单位的透明度。

## `math.random()`

[The `math.random()` function] 历史上会忽略 `$limit` 中的单位并返回无单位的值。例如 `math.random(100px)` 会丢弃 "px" 并返回一个值如 `42`。

Sass 的未来版本将停止忽略 `$limit` 参数的单位并返回具有相同单位的随机整数。

[The `math.random()` function]: /documentation/modules/math#random

{% codeExample 'function-units', false %}
  @use "sass:math";

  // Future Sass, doesn't work yet!
  @debug math.random(100px); // 42px
  ===
  @use "sass:math"

  // Future Sass, doesn't work yet!
  @debug math.random(100px)  // 42px
{% endcodeExample %}

### 阶段 1

{% compatibility 'dart: "1.54.5"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

目前，如果将带有单位的 `$limit` 传递给 `math.random()`，Sass 会发出弃用警告。

### 阶段 2

{% compatibility 'dart: false', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

在 Dart Sass 2.0.0 中，传递带有单位的 `$limit` 数字将是一个错误。

### 阶段 3

{% compatibility 'dart: false', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

在 Dart Sass 2.0.0 之后的次要版本中，传递带有单位的 `$limit` 数字给 `math.random()` 函数将再次被允许。它将返回与 `$limit` 相同单位的随机整数，而不是无单位的数字。

## 权重

[`color.mix()` function] 和 [`color.invert()` function] 历史上都忽略了其 `$weight` 参数中的单位，尽管该参数概念上表示百分比。Sass 的未来版本将需要 `%` 单位。

[`color.mix()` function]: /documentation/modules/color#mix
[`color.invert()` function]: /documentation/modules/color#invert

### 阶段 1

{% compatibility 'dart: "1.56.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

目前，如果将无单位或非 `%` 单位的 `$weight` 传递给 `color.mix()` 或 `color.invert()`，Sass 会发出弃用警告。

### 阶段 2

{% compatibility 'dart: false', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

在 Dart Sass 2.0.0 中，如果 `color.mix()` 和 `color.invert()` 接收到无单位或非 `%` 单位的 `$weight`，它们将抛出错误。

## 索引

[`list.nth()` function] 和 [`list.set-nth()` function] 历史上都忽略了其 `$n` 参数中的单位。Sass 的未来版本将禁止任何单位。

[`list.nth()` function]: /documentation/modules/list#nth
[`list.set-nth()` function]: /documentation/modules/list#set-nth

### 阶段 1

{% compatibility 'dart: "1.56.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

目前，如果将带有单位的索引 `$n` 传递给 `list.nth()` 或 `list.set-nth()`，Sass 会发出弃用警告。

### 阶段 2

{% compatibility 'dart: false', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

在 Dart Sass 2.0.0 中，如果 `list.nth()` 和 `list.set-nth()` 接收到带有单位的索引 `$n`，它们将抛出错误。

{% render 'silencing_deprecations' %}