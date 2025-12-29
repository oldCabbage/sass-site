---
title: "重大变更：空的 Alpha 通道"
introduction: |
  在 Dart Sass 1.64.3 之前，在 JS 和 Dart API 中，如果向 `SassColor` 构造函数传递 `null`，它将被视为 1。现在这种做法已弃用。用户应显式传递 1 或 `undefined`。
---

Sass 正在添加对 [CSS Color Module Level 4] 的支持。该模块中的一个变化是引入了 ["缺失组件"] 的概念：如果颜色组件（如 `alpha`）缺失，它通常被视为 0，但如果它与其他颜色插值（例如在渐变或动画中），它将自动采用另一个颜色的值。

[CSS Color Module Level 4]: https://www.w3.org/TR/css-color-4/
["缺失组件"]: https://developer.mozilla.org/en-US/docs/Web/CSS/color_value#missing_color_components

我们需要一种方法让用户通过 JS 和 Dart API 访问和设置缺失的通道，而 `null` 是最自然的方式。在大多数情况下，这不会有问题；打算创建不透明颜色的调用者通常会省略 `alpha` 参数（或在 JS 中传递 `undefined`）。但如果调用者显式传递 `null`，最终它将被视为透明颜色而不是不透明颜色。

为了保留当前行为，你只需在 `alpha` 未设置时显式传递 1。在 JS 中：

```js
new sass.SassColor({
  red: 102,
  green: 51,
  blue: 153,
  alpha: alpha ?? 1,
});
```

在 Dart 中：

```dart
sass.SassColor.rgb(102, 51, 153, alpha ?? 1);
```

{% funFact %}
  Sass API 的 TypeScript 类型已经禁止将 `null` 作为 `alpha` 传递；它只允许缺失、`undefined` 或 `Number`。但在 Dart Sass 1.64.3 之前，如果你不使用 TypeScript 并且确实传递了 `null`，它仍然被视为不透明颜色。
{% endfunFact %}

## 过渡期

{% compatibility 'dart: "1.64.3"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

在 Dart Sass 1.64.3 和即将发布的对 CSS Colors Level 4 的支持之间，Dart Sass 将继续将 `null` 的 `alpha` 值解释为不透明颜色。然而，它会发出弃用警告，以鼓励作者显式传递 `alpha` 1。