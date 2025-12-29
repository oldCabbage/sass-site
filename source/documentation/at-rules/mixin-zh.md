---
title: "@mixin 和 @include"
table_of_contents: true
introduction: >
  Mixins 允许你定义可以在样式表中重复使用的样式。它们使得避免使用非语义化的类如 `.float-left` 变得容易，并且可以将样式集合分发到库中。
---

Mixins 使用 `@mixin` 规则定义，写法为 `@mixin <name> { ... }` 或 `@mixin name(<arguments...>) { ... }`。Mixin 的名称可以是任何不以 `--` 开头的 Sass 标识符，并且可以包含任何 [语句] 除了 [顶层语句]。它们可以用来封装可以放入单个 [样式规则] 的样式；它们可以包含自己的样式规则，这些规则可以嵌套在其他规则中或包含在样式表的顶层；或者它们只是用来修改变量。

[语句]: /documentation/syntax/structure#statements
[顶层语句]: /documentation/syntax/structure#top-level-statements
[样式规则]: /documentation/style-rules

Mixins 使用 `@include` 规则包含到当前上下文中，写法为 `@include <name>` 或 `@include <name>(<arguments...>)`，其中 `<name>` 是要包含的 mixin 的名称。

{% codeExample 'mixin-include' %}
  @mixin reset-list {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  @mixin horizontal-list {
    @include reset-list;

    li {
      display: inline-block;
      margin: {
        left: -2px;
        right: 2em;
      }
    }
  }

  nav ul {
    @include horizontal-list;
  }
  ===
  @mixin reset-list
    margin: 0
    padding: 0
    list-style: none


  @mixin horizontal-list
    @include reset-list

    li
      display: inline-block
      margin:
        left: -2px
        right: 2em




  nav ul
    @include horizontal-list
{% endcodeExample %}

{% funFact %}
  Mixin 名称，像所有 Sass 标识符一样，将连字符和下划线视为相同的。这意味着 `reset-list` 和 `reset_list` 都指代相同的 mixin。这是 Sass 非常早期版本的一个遗留问题，在那时它 *只* 允许标识符名称中使用下划线。一旦 Sass 增加了对连字符的支持以匹配 CSS 的语法，这两种方式就被视为等价的以简化迁移。
{% endfunFact %}

## 参数

{% comment %}
  当更改此部分时，请不要忘记更改函数参数部分！
{% endcomment %}

Mixins 还可以接受参数，这使得每次调用时都可以自定义它们的行为。参数在 `@mixin` 规则中定义，在 mixin 名称之后，形式为括号括起来的变量名列表。然后必须使用与参数数量相同的 [SassScript 表达式][] 形式的参数包含 mixin。这些表达式的值在 mixin 的主体中作为相应的变量使用。

[SassScript 表达式]: /documentation/syntax/structure#expressions

{% codeExample 'mixin-arguments' %}
  @mixin rtl($property, $ltr-value, $rtl-value) {
    #{$property}: $ltr-value;

    [dir=rtl] & {
      #{$property}: $rtl-value;
    }
  }

  .sidebar {
    @include rtl(float, left, right);
  }
  ===
  @mixin rtl($property, $ltr-value, $rtl-value)
    #{$property}: $ltr-value

    [dir=rtl] &
      #{$property}: $rtl-value



  .sidebar
    @include rtl(float, left, right)
{% endcodeExample %}

{% funFact %}
  参数列表也可以有尾随逗号！这使得在重构样式表时更容易避免语法错误。
{% endfunFact %}

### 可选参数

通常，mixin 声明的每个参数在包含该 mixin 时都必须传递。但是，你可以通过定义一个 *默认值* 来使参数可选，如果没有传递该参数，则使用该默认值。默认值使用与 [变量声明][] 相同的语法：变量名，后跟冒号和 [SassScript 表达式][]。这使得定义灵活的 mixin API 变得容易，这些 API 可以以简单或复杂的方式使用。

[变量声明]: /documentation/variables
[SassScript 表达式]: /documentation/syntax/structure#expressions

{% codeExample 'optional-arguments' %}
  @mixin replace-text($image, $x: 50%, $y: 50%) {
    text-indent: -99999em;
    overflow: hidden;
    text-align: left;

    background: {
      image: $image;
      repeat: no-repeat;
      position: $x $y;
    }
  }

  .mail-icon {
    @include replace-text(url("/images/mail.svg"), 0);
  }
  ===
  @mixin replace-text($image, $x: 50%, $y: 50%)
    text-indent: -99999em
    overflow: hidden
    text-align: left

    background:
      image: $image
      repeat: no-repeat
      position: $x $y

  .mail-icon
    @include replace-text(url("/images/mail.svg"), 0)
{% endcodeExample %}

{% funFact %}
  默认值可以是任何 SassScript 表达式，甚至可以引用前面的参数！
{% endfunFact %}

### 关键字参数

在包含 mixin 时，除了按参数列表中的位置传递参数外，还可以通过名称传递参数。这对于具有多个可选参数的 mixin 或具有 [布尔值][] 参数的情况特别有用，这些参数没有名称时含义不明显。关键字参数使用与 [变量声明][] 和 [可选参数][] 相同的语法。

[变量声明]: /documentation/variables
[布尔值]: /documentation/values/booleans
[可选参数]: #optional-arguments

{% codeExample 'keyword-arguments' %}
  @mixin square($size, $radius: 0) {
    width: $size;
    height: $size;

    @if $radius != 0 {
      border-radius: $radius;
    }
  }

  .avatar {
    @include square(100px, $radius: 4px);
  }
  ===
  @mixin square($size, $radius: 0)
    width: $size
    height: $size

    @if $radius != 0
      border-radius: $radius



  .avatar
    @include square(100px, $radius: 4px)
{% endcodeExample %}

{% headsUp %}
  因为 *任何* 参数都可以通过名称传递，所以在重命名 mixin 的参数时要小心……它可能会破坏你的用户！在一段时间内保留旧名称作为 [可选参数][] 并在有人传递它时打印一个 [警告][]，这样他们就知道要迁移到新参数。

  [可选参数]: #optional-arguments
  [警告]: /documentation/at-rules/warn
{% endheadsUp %}

### 接受任意参数

有时让 mixin 能够接受任意数量的参数是有用的。如果 `@mixin` 声明中的最后一个参数以 `...` 结尾，那么传递给该 mixin 的所有额外参数都会作为 [列表][] 传递给该参数。这个参数被称为 [参数列表][]。

[列表]: /documentation/values/lists
[参数列表]: /documentation/values/lists#argument-lists

{% codeExample 'arbitrary-arguments' %}
  @mixin order($height, $selectors...) {
    @for $i from 0 to length($selectors) {
      #{nth($selectors, $i + 1)} {
        position: absolute;
        height: $height;
        margin-top: $i * $height;
      }
    }
  }

  @include order(150px, "input.name", "input.address", "input.zip");
  ===
  @mixin order($height, $selectors...)
    @for $i from 0 to length($selectors)
      #{nth($selectors, $i + 1)}
        position: absolute
        height: $height
        margin-top: $i * $height




  @include order(150px, "input.name", "input.address", "input.zip")
{% endcodeExample %}

#### 接受任意关键字参数

参数列表也可以用于接受任意关键字参数。[`meta.keywords()` 函数][] 接受一个参数列表并返回任何传递给 mixin 的额外关键字参数作为参数名称（不包括 `$`）到这些参数值的 [映射][]。

[`meta.keywords()` 函数]: /documentation/modules/meta#keywords
[映射]: /documentation/values/maps

{% render 'code_snippets/example-mixin-arbitrary-keyword-arguments' %}

{% funFact %}
  如果你从未向 [`meta.keywords()` 函数][] 传递参数列表，该参数列表将不允许额外的关键字参数。这有助于调用 mixin 的用户确保他们没有意外拼写错误的任何参数名称。

  [`meta.keywords()` 函数]: /documentation/modules/meta#keywords
{% endfunFact %}

#### 传递任意参数

就像参数列表允许 mixin 接受任意位置或关键字参数一样，相同的语法可以用来 *传递* 位置和关键字参数给 mixin。如果你传递一个列表后跟 `...` 作为包含的最后一个参数，其元素将被视为附加的位置参数。同样，一个映射后跟 `...` 将被视为附加的关键字参数。你甚至可以同时传递两者！

{% codeExample 'passing-arbitrary-arguments', false %}
  $form-selectors: "input.name", "input.address", "input.zip" !default;

  @include order(150px, $form-selectors...);
  ===
  $form-selectors: "input.name", "input.address", "input.zip" !default

  @include order(150px, $form-selectors...)
{% endcodeExample %}

{% funFact %}
  因为一个 [参数列表][] 同时跟踪位置参数和关键字参数，你可以将其同时传递给另一个 mixin。这使得定义一个 mixin 的别名变得超级容易！

  [参数列表]: /documentation/values/lists#argument-lists

  {% codeExample 'passing-arbitrary-arguments-fun-fact' %}
    @mixin btn($args...) {
      @warn "The btn() mixin is deprecated. Include button() instead.";
      @include button($args...);
    }
    ===
    @mixin btn($args...)
      @warn "The btn() mixin is deprecated. Include button() instead."
      @include button($args...)
  {% endcodeExample %}
{% endfunFact %}

## 内容块

除了接受参数，mixin 还可以接受一个完整的样式块，称为 *内容块*。mixin 可以通过在其主体中包含 `@content` 规则来声明它接受一个内容块。内容块使用大括号传递，就像 Sass 中的任何其他块一样，并且它会被注入到 `@content` 规则的位置。

{% codeExample 'content-blocks' %}
  @mixin hover {
    &:not([disabled]):hover {
      @content;
    }
  }

  .button {
    border: 1px solid black;
    @include hover {
      border-width: 2px;
    }
  }
  ===
  @mixin hover
    &:not([disabled]):hover
      @content



  .button
    border: 1px solid black
    @include hover
      border-width: 2px
{% endcodeExample %}

{% funFact %}
  一个 mixin 可以包含多个 `@content` 规则。如果是这样，内容块将为每个 `@content` 分别包含。
{% endfunFact %}

{% headsUp %}
  内容块是 *词法作用域* 的，这意味着它只能看到 mixin 包含时的作用域中的 [局部变量][]。它无法看到传递给它的 mixin 中定义的任何变量，即使这些变量在调用内容块之前定义。

  [局部变量]: /documentation/variables#scope
{% endheadsUp %}

### 向内容块传递参数

{% compatibility 'dart: "1.15.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

mixin 可以像传递参数给另一个 mixin 一样通过编写 `@content(<arguments...>)` 向其内容块传递参数。编写内容块的用户可以通过编写 `@include <name> using (<arguments...>)` 接受参数。内容块的参数列表就像 mixin 的参数列表一样工作，`@content` 传递给它的参数就像传递给 mixin 的参数一样工作。

{% headsUp %}
  如果 mixin 向其内容块传递参数，该内容块 *必须* 声明它接受这些参数。这意味着最好只按位置（而不是按名称）传递参数，并且这意味着传递更多参数是一个破坏性更改。

  如果你想灵活地向内容块传递信息，考虑传递一个包含它可能需要的信息的 [映射][]！

  [映射]: /documentation/values/maps
{% endheadsUp %}

{% codeExample 'passing-arguments-to-content-blocks' %}
  @mixin media($types...) {
    @each $type in $types {
      @media #{$type} {
        @content($type);
      }
    }
  }

  @include media(screen, print) using ($type) {
    h1 {
      font-size: 40px;
      @if $type == print {
        font-family: Calluna;
      }
    }
  }
  ===
  @mixin media($types...)
    @each $type in $types
      @media #{$type}
        @content($type)




  @include media(screen, print) using ($type)
    h1
      font-size: 40px
      @if $type == print
        font-family: Calluna
{% endcodeExample %}

## 缩进的 Mixin 语法

[缩进语法][] 除了标准的 `@mixin` 和 `@include` 之外，还有一种特殊的语法来定义和使用 mixin。mixin 使用字符 `=` 定义，使用 `+` 包含。虽然这种语法更简洁，但也更难一目了然，建议用户避免使用它。

[缩进语法]: /documentation/syntax#the-indented-syntax

{% codeExample 'indented-syntax', true, 'sass' %}
  =reset-list
    margin: 0
    padding: 0
    list-style: none

  =horizontal-list
    +reset-list

    li
      display: inline-block
      margin:
        left: -2px
        right: 2em

  nav ul
    +horizontal-list
{% endcodeExample %}