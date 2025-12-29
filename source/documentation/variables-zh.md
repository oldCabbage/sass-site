---
title: 变量
table_of_contents: true
introduction: >
  Sass 变量很简单：你将一个值赋给以 `$` 开头的名称，然后可以使用该名称而不是直接使用该值。尽管它们很简单，但它们是 Sass 提供的最有用的工具之一。变量使得减少重复、进行复杂计算、配置库等成为可能。
---

变量声明看起来与 [属性声明][] 非常相似：它写成 `<variable>: <expression>`。与只能在样式规则或 at-rule 中声明的属性不同，变量可以在任何你想要的地方声明。要使用一个变量，只需将其包含在值中。

[property declaration]: /documentation/style-rules/declarations

{% codeExample 'variable' %}
  $base-color: #c6538c;
  $border-dark: rgba($base-color, 0.88);

  .alert {
    border: 1px solid $border-dark;
  }
  ===
  $base-color: #c6538c
  $border-dark: rgba($base-color, 0.88)

  .alert
    border: 1px solid $border-dark
{% endcodeExample %}

{% headsUp %}
  CSS 有自己的 [变量][], 它们与 Sass 变量完全不同。要知道它们的区别！

  [variables]: /documentation/style-rules/declarations#custom-properties

  * Sass 变量在编译时会被完全移除。CSS 变量会被包含在 CSS 输出中。

  * CSS 变量可以为不同的元素具有不同的值，但 Sass 变量在同一时间只有一个值。

  * Sass 变量是 *命令式的*，这意味着如果你使用了一个变量然后更改了它的值，之前的使用会保持不变。CSS 变量是 *声明式的*，这意味着如果你更改了值，它会影响之前的使用和之后的使用。

  {% codeExample 'variable-heads-up' %}
    $variable: value 1;
    .rule-1 {
      value: $variable;
    }

    $variable: value 2;
    .rule-2 {
      value: $variable;
    }
    ===
    $variable: value 1
    .rule-1
      value: $variable


    $variable: value 2
    .rule-2
      value: $variable
  {% endcodeExample %}
{% endheadsUp %}

{% funFact %}
  Sass 变量，像所有 Sass 标识符一样，将连字符和下划线视为相同。这意味着 `$font-size` 和 `$font_size` 都指代同一个变量。这是 Sass 非常早期版本的一个遗留问题，当时它 *只* 允许在标识符名称中使用下划线。一旦 Sass 增加了对连字符的支持以匹配 CSS 的语法，两者就被视为等价的，以简化迁移。
{% endfunFact %}

## 默认值

通常当你将一个值赋给一个变量时，如果该变量已经有值，其旧值将被覆盖。但是，如果你正在编写一个 Sass 库，你可能希望允许你的用户在你使用它们生成 CSS 之前配置你的库的变量。

为了实现这一点，Sass 提供了 `!default` 标志。这只有在该变量未定义或其值为 [`null`][] 时才将一个值赋给变量。否则，将使用现有的值。

[`null`]: /documentation/values/null

### 配置模块

{% render 'doc_snippets/module-system-status' %}

使用 `!default` 定义的变量可以在使用 [`@use` 规则][] 加载模块时进行配置。Sass 库通常使用 `!default` 变量来允许它们的用户配置库的 CSS。

[`@use` 规则]: /documentation/at-rules/use

要加载带有配置的模块，编写 `@use <url> with (<variable>: <value>, <variable>: <value>)`。配置的值将覆盖变量的默认值。只有在样式表的顶层使用 `!default` 标志编写的变量才能被配置。

{% render 'code_snippets/example-use-with' %}

## 内置变量

由 [内置模块][] 定义的变量不能被修改。

[内置模块]: /documentation/modules

{% codeExample 'built-in-variables', false %}
  @use "sass:math" as math;

  // 这个赋值将会失败。
  math.$pi: 0;
  ===
  @use "sass:math" as math

  // 这个赋值将会失败。
  math.$pi: 0
{% endcodeExample %}

## 作用域

在样式表顶层声明的变量是 *全局* 的。这意味着在声明之后，它们可以在它们的模块中的任何地方访问。但这并不适用于所有变量。在块（SCSS 中的花括号或 Sass 中的缩进代码）中声明的变量通常是 *局部* 的，并且只能在其声明的块内访问。

{% codeExample 'scope' %}
  $global-variable: global value;

  .content {
    $local-variable: local value;
    global: $global-variable;
    local: $local-variable;
  }

  .sidebar {
    global: $global-variable;

    // 这将会失败，因为 $local-variable 不在作用域内：
    // local: $local-variable;
  }
  ===
  $global-variable: global value

  .content
    $local-variable: local value
    global: $global-variable
    local: $local-variable


  .sidebar
    global: $global-variable

    // 这将会失败，因为 $local-variable 不在作用域内：
    // local: $local-variable
{% endcodeExample %}

### 遮蔽

局部变量甚至可以与全局变量具有相同的名称。如果发生这种情况，实际上有两个同名的不同变量：一个局部变量和一个全局变量。这有助于确保编写局部变量的作者不会意外地更改他们甚至都不知道的全局变量的值。

{% codeExample 'shadowing' %}
  $variable: global value;

  .content {
    $variable: local value;
    value: $variable;
  }

  .sidebar {
    value: $variable;
  }
  ===
  $variable: global value

  .content
    $variable: local value
    value: $variable


  .sidebar
    value: $variable
{% endcodeExample %}

如果你需要从局部作用域（例如在 mixin 中）设置全局变量的值，可以使用 `!global` 标志。带有 `!global` 标志的变量声明 *总是* 会赋值给全局作用域。

{% codeExample 'global-variable' %}
  $variable: first global value;

  .content {
    $variable: second global value !global;
    value: $variable;
  }

  .sidebar {
    value: $variable;
  }
  ===
  $variable: first global value

  .content
    $variable: second global value !global
    value: $variable


  .sidebar
    value: $variable
{% endcodeExample %}

{% headsUp %}
  {% compatibility 'dart: "2.0.0"', 'libsass: false', 'ruby: false' %}
    较旧的 Sass 版本允许 `!global` 用于尚未存在的变量。这种行为已被弃用，以确保每个样式表声明的变量在任何情况下都相同。
  {% endcompatibility %}

  `!global` 标志只能用于设置已经在其文件顶层声明的变量。它 *不能* 用于声明新变量。
{% endheadsUp %}

### 流程控制作用域

在 [流程控制规则][] 中声明的变量具有特殊的作用域规则：它们不会遮蔽与流程控制规则同一级别的变量。相反，它们只是赋值给这些变量。这使得有条件地为变量赋值或在循环中构建值变得更加容易。

[流程控制规则]: /documentation/at-rules/control

{% codeExample 'flow-control' %}
  $dark-theme: true !default;
  $primary-color: #f8bbd0 !default;
  $accent-color: #6a1b9a !default;

  @if $dark-theme {
    $primary-color: darken($primary-color, 60%);
    $accent-color: lighten($accent-color, 60%);
  }

  .button {
    background-color: $primary-color;
    border: 1px solid $accent-color;
    border-radius: 3px;
  }
  ===
  $dark-theme: true !default
  $primary-color: #f8bbd0 !default
  $accent-color: #6a1b9a !default

  @if $dark-theme
    $primary-color: darken($primary-color, 60%)
    $accent-color: lighten($accent-color, 60%)


  .button
    background-color: $primary-color
    border: 1px solid $accent-color
    border-radius: 3px
{% endcodeExample %}

{% headsUp %}
  流程控制作用域中的变量可以为外部作用域中的现有变量赋值，但在流程控制作用域中声明的新变量在外部作用域中是不可访问的。请确保在赋值之前已经声明了变量，即使需要将其声明为 `null`。
{% endheadsUp %}

## 高级变量函数

Sass 核心库提供了一些用于处理变量的高级函数。[`meta.variable-exists()` 函数][] 返回当前作用域中是否存在具有给定名称的变量，而 [`meta.global-variable-exists()` 函数][] 仅用于全局作用域。

[`meta.variable-exists()` 函数]: /documentation/modules/meta#variable-exists
[`meta.global-variable-exists()` 函数]: /documentation/modules/meta#global-variable-exists

{% headsUp %}
  用户有时希望使用插值来根据另一个变量定义一个变量名。Sass 不允许这样做，因为它使得很难一目了然地知道哪些变量在何处定义。不过，你可以定义一个从名称到值的 [映射][]，然后使用变量来访问它。

  [映射]: /documentation/values/maps

  {% codeExample 'advanced-variable-functions' %}
    @use "sass:map";

    $theme-colors: (
      "success": #28a745,
      "info": #17a2b8,
      "warning": #ffc107,
    );

    .alert {
      // 代替 $theme-color-#{warning}
      background-color: map.get($theme-colors, "warning");
    }
    ===
    @use "sass:map"

    $theme-colors: ("success": #28a745, "info": #17a2b8, "warning": #ffc107)

    .alert
      // 代替 $theme-color-#{warning}
      background-color: map.get($theme-colors, "warning")
  {% endcodeExample %}
{% endheadsUp %}
