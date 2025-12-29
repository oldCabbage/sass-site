---
title: 语法
introduction: >
  Sass 支持两种不同的语法。每一种都可以加载另一种，因此选择哪种语法由你和你的团队决定。
---

## SCSS

SCSS 语法使用文件扩展名 `.scss`。除了少数例外，它是一个 CSS 的超集，这意味着基本上 **所有有效的 CSS 也是有效的 SCSS**。由于它与 CSS 的相似性，这是最容易上手的语法，也是最流行的。

SCSS 看起来像这样：

```scss
@mixin button-base() {
  @include typography(button);
  @include ripple-surface;
  @include ripple-radius-bounded;

  display: inline-flex;
  position: relative;
  height: $button-height;
  border: none;
  vertical-align: middle;

  &:hover {
    cursor: pointer;
  }

  &:disabled {
    color: $mdc-button-disabled-ink-color;
    cursor: default;
    pointer-events: none;
  }
}
```

## 缩进语法

缩进语法是 Sass 的原始语法，因此它使用文件扩展名 `.sass`。由于这个扩展名，它有时也被称为 "Sass"。缩进语法支持与 SCSS 相同的所有功能，但它使用缩进而不是大括号和分号来描述文档的格式。

一般来说，任何你会在 CSS 或 SCSS 中写大括号的地方，在缩进语法中只需缩进一层。任何一行在语句可以结束的地方结束，这相当于一个分号。缩进语法中还有一些其他差异，这些差异在参考文档中有所说明。

缩进语法看起来像这样：

```sass
@mixin button-base()
  @include typography(button)
  @include ripple-surface
  @include ripple-radius-bounded

  display: inline-flex
  position: relative
  height: $button-height
  border: none
  vertical-align: middle

  &:hover
    cursor: pointer

  &:disabled
    color: $mdc-button-disabled-ink-color
    cursor: default
    pointer-events: none
```

### 多行语句

{% compatibility 'dart: "1.84.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

在缩进语法中，语句可以跨越多行，只要换行发生在语句不能结束的地方。这包括括号或其他括号内部，或者 Sass 特定的 @-rule 中的关键字之间。

```sass
.grid
  display: grid
  grid-template: (
    "header" min-content
    "main" 1fr
  )

@for 
  $i from 
  1 through 3
    ul:nth-child(3n + #{$i})
      margin-left: $i * 10
```