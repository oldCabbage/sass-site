---
title: "@at-root"
introduction: >
  `@at-root` 规则通常写成 `@at-root <selector> { ... }`，并导致其中的所有内容在文档的根部输出，而不是使用正常的嵌套。它最常用于与 [SassScript 父选择器](/documentation/style-rules/parent-selector#in-sassscript) 和 [选择器函数](/documentation/modules/selector) 一起进行[高级嵌套](/documentation/style-rules/parent-selector#advanced-nesting)。
---

{% render 'code_snippets/example-advanced-nesting' %}

这里需要 `@at-root` 规则是因为 Sass 在执行选择器嵌套时不知道用于生成选择器的插值是什么。这意味着即使你使用 `&` 作为 SassScript 表达式，它也会自动将外部选择器添加到内部选择器。`@at-root` 明确告诉 Sass 不要包含外部选择器（尽管它总是会包含在 `&` 中作为表达式）。

{% funFact %}
  `@at-root` 规则也可以写成 `@at-root { ... }` 来将多个样式规则放在文档的根部。实际上，`@at-root <selector> { ... }` 只是 `@at-root { <selector> { ... } }` 的简写！
{% endfunFact %}

## 超出样式规则

单独使用时，`@at-root` 只会去除[样式规则][style rules]。任何像 [`@media`][] 或 [`@supports`][] 这样的 at-rules 都会被保留。不过，如果你不希望这样，可以使用类似于[媒体查询功能][media query features]的语法来控制它包含或排除的内容，写成 `@at-root (with: <rules...>) { ... }` 或 `@at-root (without: <rules...>) { ... }`。`(without: ...)` 查询告诉 Sass 哪些规则应该被排除；`(with: ...)` 查询排除所有规则*除了*列出的那些。

[style rules]: /documentation/style-rules
[`@media`]: /documentation/at-rules/css#media
[`@supports`]: /documentation/at-rules/css#supports
[media query features]: https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries#Targeting_media_features

{% codeExample 'at-root' %}
  @media print {
    .page {
      width: 8in;

      @at-root (without: media) {
        color: #111;
      }

      @at-root (with: rule) {
        font-size: 1.2em;
      }
    }
  }
  ===
  @media print
    .page
      width: 8in

      @at-root (without: media)
        color: #111


      @at-root (with: rule)
        font-size: 1.2em
{% endcodeExample %}

除了 at-rules 的名称，查询中还可以使用两个特殊值：

* `rule` 指样式规则。例如，`@at-root (with: rule)` 排除所有 at-rules 但保留样式规则。

* `all` 指所有 at-rules *和* 样式规则都应该被排除。
