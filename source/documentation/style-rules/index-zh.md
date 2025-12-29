---
title: 样式规则
table_of_contents: true
introduction: >
  样式规则是 Sass 的基础，就像它们是 CSS 的基础一样。它们的工作方式也相同：你选择要使用选择器样式的元素，并声明影响这些元素外观的[属性](/documentation/style-rules/declarations)。
---

{% codeExample 'style-rules' %}
  .button {
    padding: 3px 10px;
    font-size: 12px;
    border-radius: 3px;
    border: 1px solid #e1e4e8;
  }
  ===
  .button
    padding: 3px 10px
    font-size: 12px
    border-radius: 3px
    border: 1px solid #e1e4e8
{% endcodeExample %}

## 嵌套

但 Sass 想要让你的生活更轻松。与其一遍又一遍地重复相同的选择器，你可以在一个样式规则中编写另一个样式规则。Sass 会自动将外部规则的选择器与内部规则的选择器组合在一起。

{% render 'code_snippets/example-nesting' %}

{% headsUp %}
  嵌套规则非常有用，但也可能导致难以可视化实际生成的 CSS 量。嵌套越深，为 CSS 提供服务所需的带宽就越多，浏览器渲染它所需的工作量也越大。保持这些选择器浅一些！
{% endheadsUp %}

### 选择器列表

嵌套规则在处理选择器列表（即用逗号分隔的选择器）时非常聪明。每个复杂选择器（逗号之间的选择器）都会单独嵌套，然后它们会被重新组合成一个选择器列表。

{% codeExample 'selector-lists' %}
  .alert, .warning {
    ul, p {
      margin-right: 0;
      margin-left: 0;
      padding-bottom: 0;
    }
  }
  ===
  .alert, .warning
    ul, p
      margin-right: 0
      margin-left: 0
      padding-bottom: 0
{% endcodeExample %}

### 选择器组合器

你也可以嵌套使用[组合器][]的选择器。你可以在外部选择器的末尾、内部选择器的开头，甚至在两者之间单独使用组合器。

[combinators]: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors#Combinators#Combinators

{% codeExample 'selector-combinators' %}
  ul > {
    li {
      list-style-type: none;
    }
  }

  h2 {
    + p {
      border-top: 1px solid gray;
    }
  }

  p {
    ~ {
      span {
        opacity: 0.8;
      }
    }
  }
  ===
  ul >
    li
      list-style-type: none



  h2
    + p
      border-top: 1px solid gray



  p
    ~
      span
        opacity: 0.8
{% endcodeExample %}

### 高级嵌套

如果你想要比仅仅使用后代组合器（即普通的空格）将嵌套样式规则按顺序组合更多的功能，Sass 会为你提供帮助。有关更多详细信息，请参阅[父选择器文档][]。

[parent selector documentation]: /documentation/style-rules/parent-selector

## 插值

你可以使用[插值][]将来自[表达式][]（如变量和函数调用）的值注入选择器中。这在编写[mixins][]时特别有用，因为它允许你从用户传递的参数创建选择器。

[interpolation]: /documentation/interpolation
[expressions]: /documentation/syntax/structure#expressions
[mixins]: /documentation/at-rules/mixin

{% codeExample 'interpolation' %}
  @mixin define-emoji($name, $glyph) {
    span.emoji-#{$name} {
      font-family: IconFont;
      font-variant: normal;
      font-weight: normal;
      content: $glyph;
    }
  }

  @include define-emoji("women-holding-hands", "👭");
  ===
  @mixin define-emoji($name, $glyph)
    span.emoji-#{$name}
      font-family: IconFont
      font-variant: normal
      font-weight: normal
      content: $glyph



  @include define-emoji("women-holding-hands", "👭")
{% endcodeExample %}

{% funFact %}
  Sass 只在插值解析之后解析选择器。这意味着你可以安全地使用插值生成选择器的任何部分，而不必担心它无法解析。
{% endfunFact %}

你可以将插值与父选择器 `&`、[`@at-root` 规则][]和[选择器函数][]结合使用，以在动态生成选择器时发挥强大的作用。有关更多信息，请参阅[父选择器文档][]。

[`@at-root` rule]: /documentation/at-rules/at-root
[selector functions]: /documentation/modules/selector
[parent selector documentation]: /documentation/style-rules/parent-selector