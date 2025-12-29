---
title: '重大变更: Plain-CSS if()'
introduction: >
  Sass 的旧版 `if()` 函数将被弃用，取而代之的是官方 CSS 的 `if()` 函数语法。此语法允许 Sass 和 CSS 条件自由混合。
---

{% compatibility 'dart: "1.95.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

2010年，就在添加了 [布尔值类型] 之后不久，Sass 添加了全局 `if()` 函数，作为一种在单个表达式中轻松使用布尔值的方法，而无需编写整个 [`@if` 规则]。此函数的签名是 `if($condition, $if-true, $if-false)`，如果 `$condition` 是 [真值]，则返回 `$if-true`，否则返回 `$if-false`。

[布尔值类型]: /documentation/values/booleans
[`@if` 规则]: /documentation/at-rules/control/if
[真值]: /documentation/at-rules/control/if#truthiness-and-falsiness

当时，浏览器甚至不支持 `@media` 查询，我们从未想过 CSS 有一天可能支持自己的 `if()` 函数。但十五年后，[CSS `if()` 函数] 的支持开始在浏览器中落地，我们也必须这样做以保持完全的 CSS 兼容性。

[CSS `if()` 函数]: https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/if

Sass 现在支持 [plain-CSS `if()` 语法]，以及一个特殊的 `sass(...)` 条件，用于评估 Sass 表达式。为了避免冗余并统一到最兼容 CSS 的选项，我们计划最终从语言中移除旧版 `if()` 函数。

[plain-CSS `if()` 语法]: /documentation/syntax/special-functions#if

您可以使用 [the Sass migrator] 自动从旧版 `if()` 函数迁移到 CSS `if()` 语法。

[the Sass migrator]: https://sass-lang.com/documentation/cli/migrator/#if

{% codeExample 'debug', false %}
  @use 'sass:meta';

  // Instead of if(true, 10px, 15px)
  @debug if(sass(true): 10px; else: 15px);

  // Instead of if(meta.variable-defined($var), $var, null)
  @debug if(sass(meta.variable-defined($var)): $var);
  ===
  @use 'sass:meta'

  // Instead of if(true, 10px, 15px)
  @debug if(sass(true): 10px; else: 15px)

  // Instead of if(meta.variable-defined($var), $var, null)
  @debug if(sass(meta.variable-defined($var)): $var)
{% endcodeExample %}


{% render 'silencing_deprecations' %}