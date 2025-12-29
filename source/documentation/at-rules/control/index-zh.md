---
title: 流程控制规则
introduction: >
  Sass 提供了多个 at-rules，使您能够控制样式是否被输出，或者以小的差异多次输出它们。它们还可以在 [mixins](/documentation/at-rules/mixin) 和 [functions](/documentation/at-rules/function) 中使用，以编写小型算法，使编写 Sass 更加轻松。Sass 支持四种流程控制规则。
---

- [`@if`](/documentation/at-rules/control/if) 控制一个代码块是否被评估。

- [`@each`](/documentation/at-rules/control/each) 对 [list][] 中的每个元素或 [map][] 中的每对键值对评估一个代码块。

- [`@for`](/documentation/at-rules/control/for) 根据指定的次数评估一个代码块。

- [`@while`](/documentation/at-rules/control/while) 在满足某个条件之前持续评估一个代码块。

[list]: /documentation/values/lists
[map]: /documentation/values/maps
