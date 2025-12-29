---
title: At-Rules
introduction: >
  Sass 的许多额外功能以 CSS 之上的新 [at-rules](https://developer.mozilla.org/en-US/docs/Web/CSS/At-rule) 形式提供：
---

- [`@use`](/documentation/at-rules/use) 从其他 Sass 样式表加载 mixins、函数和变量，并将多个样式表的 CSS 结合在一起。

- [`@forward`](/documentation/at-rules/forward) 加载一个 Sass 样式表，并在其使用 `@use` 规则加载时使其中的 mixins、函数和变量可用。

- [`@import`](/documentation/at-rules/import) 扩展 CSS at-rule 以从其他样式表加载样式、mixins、函数和变量。

- [`@mixin` and `@include`](/documentation/at-rules/mixin) 使重用样式块变得容易。

- [`@function`](/documentation/at-rules/function) 定义可以在 [SassScript expressions][] 中使用的自定义函数。

- [`@extend`](/documentation/at-rules/extend) 允许选择器继承彼此的样式。

- [`@at-root`](/documentation/at-rules/at-root) 将其中的样式放在 CSS 文档的根部。

- [`@error`](/documentation/at-rules/error) 使编译因错误消息而失败。

- [`@warn`](/documentation/at-rules/warn) 打印警告而不完全停止编译。

- [`@debug`](/documentation/at-rules/debug) 打印调试目的的消息。

- 流程控制规则如 [`@if`][], [`@each`][], [`@for`][], 和 [`@while`][] 控制样式发出的次数或是否发出。

[SassScript expressions]: /documentation/syntax/structure#expressions
[`@if`]: /documentation/at-rules/control/if
[`@each`]: /documentation/at-rules/control/each
[`@for`]: /documentation/at-rules/control/for
[`@while`]: /documentation/at-rules/control/while

Sass 还对 [plain CSS at-rules][] 有一些特殊行为：它们可以包含 [interpolation][]，并且可以嵌套在样式规则中。其中一些，如 [`@media`][] 和 [`@supports`][]，还允许直接在规则本身中使用 SassScript 而无需插值。

[plain CSS at-rules]: /documentation/at-rules/css
[interpolation]: /documentation/interpolation
[`@media`]: /documentation/at-rules/css#media
[`@supports`]: /documentation/at-rules/css#supports