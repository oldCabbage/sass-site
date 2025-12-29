---
title: '重大变更: CSS 变量语法'
introduction: >
  LibSass 和 Ruby Sass 的旧版本像解析任何其他属性声明一样解析自定义属性声明，允许使用 [SassScript 表达式](/documentation/syntax/structure#expressions) 作为值。但这种做法与 CSS 不兼容。
---

{% compatibility 'dart: true', 'libsass: "3.5.0"', 'ruby: "3.5.0"' %}{% endcompatibility %}

CSS 规范允许在自定义属性声明中使用几乎任何字符字符串。尽管这些值对任何 CSS 属性可能没有意义，但可以通过 JavaScript 访问它们。当它们被解析为 SassScript 值时，本应有效的纯 CSS 语法将无法解析。例如，[Polymer 库][] 使用这种方法支持纯 CSS 混合宏：

[Polymer 库]: https://polymer-library.polymer-project.org/3.0/docs/devguide/custom-css-properties#use-custom-css-mixins

{% codeExample 'css-vars', true, 'scss' %}
  :root {
    --flex-theme: {
      border: 1px solid var(--theme-dark-blue);
      font-family: var(--theme-font-family);
      padding: var(--theme-wide-padding);
      background-color: var(--theme-light-blue);
    };
  }
{% endcodeExample %}

为了最大程度地兼容纯 CSS，Sass 的较新版本要求自定义属性值中的 SassScript 表达式必须写在 [插值](/documentation/interpolation) 中。插值也可以在旧版本的 Sass 中工作，因此建议在所有样式表中使用。

{% codeExample 'css-vars-interpolation' %}
  $accent-color: #fbbc04;

  :root {
    // 错误，将在新版本的 Sass 中不起作用。
    --accent-color-wrong: $accent-color;

    // 正确，将在所有 Sass 版本中起作用。
    --accent-color-right: #{$accent-color};
  }
  ===
  $accent-color: #fbbc04

  :root
    // 错误，将在新版本的 Sass 中不起作用。
    --accent-color-wrong: $accent-color

    // 正确，将在所有 Sass 版本中起作用。
    --accent-color-right: #{$accent-color}
{% endcodeExample %}

{% headsUp %}
  因为插值会移除带引号字符串的引号，可能需要使用 [`meta.inspect()` 函数][] 来保留它们的引号。

  [`meta.inspect()` 函数]: /documentation/modules/meta#inspect

  {% codeExample 'css-vars-heads-up' %}
    @use "sass:meta";

    $font-family-monospace: Menlo, Consolas, "Courier New", monospace;

    :root {
      --font-family-monospace: #{meta.inspect($font-family-monospace)};
    }
    ===
    @use "sass:meta"

    $font-family-monospace: Menlo, Consolas, "Courier New", monospace

    :root
      --font-family-monospace: #{meta.inspect($font-family-monospace)}
  {% endcodeExample %}
{% endheadsUp %}