---
title: 列表
table_of_contents: true
---

{% compatibility 'dart: true', 'libsass: "3.5.0"', 'ruby: "3.5.0"', 'feature: "Square Brackets"' %}
  较旧的 LibSass 和 Ruby Sass 实现不支持使用方括号的列表。
{% endcompatibility %}

列表包含其他值的序列。在 Sass 中，列表中的元素可以用逗号（`Helvetica, Arial, sans-serif`）、空格（`10px 15px 0 0`）或 [斜杠] 分隔，只要在列表中保持一致即可。与大多数其他语言不同，Sass 中的列表不需要特殊的括号；任何用空格或逗号分隔的 [表达式] 都被视为列表。然而，你可以使用方括号编写列表（`[line1 line2]`），这对于使用 [`grid-template-columns`] 非常有用。

[slashes]: #slash-separated-lists
[expressions]: /documentation/syntax/structure#expressions
[`grid-template-columns`]: https://developer.mozilla.org/en-US/docs/Web/CSS/grid-template-columns

在不使用括号编写列表时，你可以使用括号将列表嵌套在彼此之间，或者区分列表分隔符和其他使用空格或逗号的情况。例如，`(1, 2), (3, 4)` 是一个包含两个列表的列表，每个列表包含两个数字；而 `adjust-font-stack((Helvetica, Arial, sans-serif))` 将包含三个字体名称的单个参数传递给 `adjust-font-stack` 函数。

Sass 列表可以包含一个或零个元素。单个元素的列表可以写成 `(<expression>,)` 或 `[<expression>]`，零个元素的列表可以写成 `()` 或 `[]`。此外，所有 [列表函数][] 都会将不在列表中的单个值视为包含该值的列表，这意味着你很少需要显式创建单个元素的列表。

[list functions]: /documentation/modules/list

{% headsUp %}
  没有括号的空列表不是有效的 CSS，因此 Sass 不允许你在属性值中使用它。
{% endheadsUp %}

## 斜杠分隔的列表

Sass 中的列表可以使用斜杠分隔，以表示类似于 `font: 12px/30px` 的简写来设置 `font-size` 和 `line-height` 或 `hsl(80 100% 50% / 0.5)` 语法来创建具有给定不透明度值的颜色。但是，**目前无法直接编写斜杠分隔的列表。** Sass 历史上使用 `/` 字符表示除法，因此在现有样式表过渡到使用 [`math.div()`] 之前，斜杠分隔的列表只能使用 [`list.slash()`] 编写。

[`math.div()`]: /documentation/modules/math#div
[`list.slash()`]: /documentation/modules/list#slash

更多详情，请参见 [重大更改：斜杠作为除法]。

[重大更改：斜杠作为除法]: /documentation/breaking-changes/slash-div

## 使用列表

Sass 提供了一些 [函数][]，使你可以使用列表编写强大的样式库，或者使应用程序的样式表更清晰、更易于维护。

[functions]: /documentation/modules/list

### 索引

这些函数中的许多函数接受或返回数字，称为 *索引*，这些数字引用列表中的元素。索引 1 表示列表的第一个元素。请注意，这与许多编程语言不同，这些语言中的索引从 0 开始！Sass 还使引用列表的末尾变得容易。索引 -1 表示列表的最后一个元素，-2 表示倒数第二个元素，依此类推。

### 访问元素

如果无法从列表中获取值，列表将毫无用处。你可以使用 [`list.nth($list, $n)` 函数][] 获取列表中给定索引处的元素。第一个参数是列表本身，第二个参数是要获取的值的索引。

[`list.nth($list, $n)` 函数]: /documentation/modules/list#nth

{% render 'code_snippets/example-list-nth' %}

### 对每个元素执行操作

这实际上并没有使用函数，但仍然是使用列表的最常见方式之一。[`@each` 规则][] 对列表中的每个元素评估一段样式，并将该元素分配给一个变量。

[`@each` 规则]: /documentation/at-rules/control/each

{% render 'code_snippets/example-each-list' %}

### 向列表添加元素

向列表中添加元素也很有用。[`list.append($list, $val)` 函数][] 接受一个列表和一个值，并返回一个在末尾添加了该值的列表的副本。请注意，由于 Sass 列表是 [不可变的][]，它不会修改原始列表。

[`list.append($list, $val)` 函数]: /documentation/modules/list#append
[不可变的]: #immutability

{% codeExample 'lists', false %}
  @debug append(10px 12px 16px, 25px); // 10px 12px 16px 25px
  @debug append([col1-line1], col1-line2); // [col1-line1, col1-line2]
  ===
  @debug append(10px 12px 16px, 25px)  // 10px 12px 16px 25px
  @debug append([col1-line1], col1-line2)  // [col1-line1, col1-line2]
{% endcodeExample %}

### 在列表中查找元素

如果你需要检查某个元素是否在列表中或找出它的索引，可以使用 [`list.index($list, $value)` 函数][]。这接受一个列表和要在该列表中定位的值，并返回该值的索引。

[`list.index($list, $value)` 函数]: /documentation/modules/list#index

{% render 'code_snippets/example-list-index' %}

如果该值根本不在列表中，`list.index()` 将返回 [`null`][]。因为 `null` 是 [假值][]，你可以使用 `list.index()` 与 [`@if`][] 或 [`if()`] 一起检查列表是否包含给定的值。

[`null`]: /documentation/values/null
[假值]: /documentation/at-rules/control/if#truthiness-and-falsiness
[`@if`]: /documentation/at-rules/control/if
[`if()`]: /documentation/syntax/special-functions#if

{% codeExample 'list-index', false %}
  @use "sass:list";

  $valid-sides: top, bottom, left, right;

  @mixin attach($side) {
    @if not list.index($valid-sides, $side) {
      @error "#{$side} is not a valid side. Expected one of #{$valid-sides}.";
    }

    // ...
  }
  ===
  @use "sass:list"

  $valid-sides: top, bottom, left, right

  @mixin attach($side)
    @if not list.index($valid-sides, $side)
      @error "#{$side} is not a valid side. Expected one of #{$valid-sides}."


    // ...
{% endcodeExample %}

## 不可变性

Sass 中的列表是 *不可变的*，这意味着列表值的内容永远不会改变。Sass 的列表函数都返回新列表而不是修改原始列表。不可变性有助于避免许多当相同列表在样式表的不同部分共享时可能会出现的细微错误。

尽管如此，你仍然可以通过将新列表赋值给同一变量来随着时间更新状态。这通常在函数和混合宏中使用，以将多个值收集到一个列表中。

{% codeExample 'immutability', false %}
  @use "sass:list";
  @use "sass:map";

  $prefixes-by-browser: ("firefox": moz, "safari": webkit, "ie": ms);

  @function prefixes-for-browsers($browsers) {
    $prefixes: ();
    @each $browser in $browsers {
      $prefixes: list.append($prefixes, map.get($prefixes-by-browser, $browser));
    }
    @return $prefixes;
  }

  @debug prefixes-for-browsers("firefox" "ie"); // moz ms
  ===
  @use "sass:list"
  @use "sass:map"

  $prefixes-by-browser: ("firefox": moz, "safari": webkit, "ie": ms)

  @function prefixes-for-browsers($browsers)
    $prefixes: ()
    @each $browser in $browsers
      $prefixes: list.append($prefixes, map.get($prefixes-by-browser, $browser))

    @return $prefixes


  @debug prefixes-for-browsers("firefox" "ie")  // moz ms
{% endcodeExample %}

## 参数列表

当你声明一个接受 [任意参数][] 的混合宏或函数时，你得到的值是一个特殊的列表，称为 *参数列表*。它就像一个包含传递给混合宏或函数的所有参数的列表，具有一个额外的功能：如果用户传递了关键字参数，可以通过将参数列表传递给 [`meta.keywords()` 函数][] 将其作为映射访问。

[任意参数]: /documentation/at-rules/mixin#taking-arbitrary-arguments
[`meta.keywords()` 函数]: /documentation/modules/meta#keywords

{% render 'code_snippets/example-mixin-arbitrary-keyword-arguments' %}