---
title: 占位符选择器
introduction: >
  Sass 有一种特殊的选择器称为“占位符”。它看起来和作用类似于类选择器，但以 `%` 开头，并且不会包含在 CSS 输出中。事实上，任何包含占位符选择器的复杂选择器（逗号之间的选择器）都不会包含在 CSS 中，同样，所有选择器都包含占位符的选择规则也不会包含在内。
---

{% render 'code_snippets/example-placeholder' %}

一个不会被输出的选择器有什么用？它仍然可以被 [extended][]！
与类选择器不同，如果占位符没有被扩展，它们不会使 CSS 变得杂乱，并且不会强制使用库的用户在 HTML 中使用特定的类名。

[extended]: /documentation/at-rules/extend

{% codeExample 'extended-selector' %}
  %toolbelt {
    box-sizing: border-box;
    border-top: 1px rgba(#000, .12) solid;
    padding: 16px 0;
    width: 100%;

    &:hover { border: 2px rgba(#000, .5) solid; }
  }

  .action-buttons {
    @extend %toolbelt;
    color: #4285f4;
  }

  .reset-buttons {
    @extend %toolbelt;
    color: #cddc39;
  }
  ===
  %toolbelt
    box-sizing: border-box
    border-top: 1px rgba(#000, .12) solid
    padding: 16px 0
    width: 100%

    &:hover
      border: 2px rgba(#000, .5) solid

  .action-buttons
    @extend %toolbelt
    color: #4285f4


  .reset-buttons
    @extend %toolbelt
    color: #cddc39
{% endcodeExample %}

占位符选择器在编写 Sass 库时非常有用，其中每个样式规则可能会或可能不会被使用。通常来说，如果你只是为自己的应用程序编写样式表，如果有一个类选择器可用，直接扩展类选择器通常更好。