---
title: 数值运算符
table_of_contents: true
introduction: >
  Sass 支持标准的数学运算符用于
  [数值](/documentation/values/numbers)。它们会自动在兼容的单位之间进行转换。
---

* `<expression> + <expression>` 将第一个 [expression][] 的值加到第二个的值上。
* `<expression> - <expression>` 从第二个 [expression][] 的值中减去第一个的值。
* `<expression> * <expression>` 将第一个 [expression][] 的值乘以第二个的值。
* `<expression> % <expression>` 返回第一个 [expression][] 的值除以第二个的值的余数。这被称为 [*取模* 运算符][]。

[expression]: /documentation/syntax/structure#expressions
[*取模* 运算符]: https://en.wikipedia.org/wiki/Modulo_operation

{% codeExample 'numeric', false %}
  @debug 10s + 15s; // 25s
  @debug 1in - 10px; // 0.8958333333in
  @debug 5px * 3px; // 15px*px
  @debug 1in % 9px; // 0.0625in
  ===
  @debug 10s + 15s  // 25s
  @debug 1in - 10px  // 0.8958333333in
  @debug 5px * 3px  // 15px*px
  @debug 1in % 9px  // 0.0625in
{% endcodeExample %}

无单位的数值可以与任何单位的数值一起使用。

{% codeExample 'unitless-numbers', false %}
  @debug 100px + 50; // 150px
  @debug 4s * 10; // 40s
  ===
  @debug 100px + 50  // 150px
  @debug 4s * 10  // 40s
{% endcodeExample %}

单位不兼容的数值不能用于加法、减法或取模运算。

{% codeExample 'incompatible-units', false %}
  @debug 100px + 10s;
  //     ^^^^^^^^^^^
  // Error: Incompatible units px and s.
  ===
  @debug 100px + 10s
  //     ^^^^^^^^^^^
  // Error: Incompatible units px and s.
{% endcodeExample %}

## 一元运算符

你也可以将 `+` 和 `-` 作为一元运算符使用，它们只接受一个值：

* `+<expression>` 返回表达式的值而不做任何更改。
* `-<expression>` 返回表达式的值的负数版本。

{% codeExample 'unary-operators', false %}
  @debug +(5s + 7s); // 12s
  @debug -(50px + 30px); // -80px
  @debug -(10px - 15px); // 5px
  ===
  @debug +(5s + 7s)  // 12s
  @debug -(50px + 30px)  // -80px
  @debug -(10px - 15px)  // 5px
{% endcodeExample %}

{% headsUp %}
  因为 `-` 可以表示减法和一元取反，所以在空格分隔的列表中可能会混淆它们的含义。为了安全起见：

  * 在减法中，始终在 `-` 的两边写空格。
  * 在负数或一元取反前写空格，但后面不写。
  * 如果在一元取反在空格分隔的列表中，将其用括号括起来。

  Sass 中 `-` 的不同含义的优先级如下：

  1. `-` 作为标识符的一部分。唯一例外是单位；Sass 通常允许任何有效的标识符作为标识符使用，但单位不能包含连字符后跟数字。
  2. `-` 在表达式和没有空格的字面量数字之间，解析为减法。
  3. `-` 在字面量数字的开头，解析为负数。
  4. `-` 在两个数字之间，无论是否有空格，解析为减法。
  5. `-` 在字面量数字以外的值前，解析为一元取反。

  {% codeExample 'heads-up-subtraction-unary-negation', false %}
    @debug a-1; // a-1
    @debug 5px-3px; // 2px
    @debug 5-3; // 2
    @debug 1 -2 3; // 1 -2 3

    $number: 2;
    @debug 1 -$number 3; // -1 3
    @debug 1 (-$number) 3; // 1 -2 3
    ===
    @debug a-1  // a-1
    @debug 5px-3px  // 2px
    @debug 5-3  // 2
    @debug 1 -2 3  // 1 -2 3

    $number: 2
    @debug 1 -$number 3  // -1 3
    @debug 1 (-$number) 3  // 1 -2 3
  {% endcodeExample %}
{% endheadsUp %}

## 除法

{% compatibility 'dart: "1.33.0"', 'libsass: false', 'ruby: false', 'feature: "math.div()"' %}{% endcompatibility %}

与其他数学运算不同，Sass 中的除法使用 [`math.div()`] 函数。虽然许多编程语言使用 `/` 作为除法运算符，但在 CSS 中 `/` 用作分隔符（如 `font: 15px/32px` 或 `hsl(120 100% 50% / 0.8)`）。虽然 Sass 支持使用 `/` 作为除法运算符，但这已被弃用，并将在未来的版本中[移除]。

[`math.div()`]: /documentation/modules/math#div
[移除]: /documentation/breaking-changes/slash-div

### 斜杠分隔的值

在 Sass 仍然支持 `/` 作为除法运算符的情况下，它必须有一种方法来区分 `/` 作为分隔符和 `/` 作为除法运算符。为了使这起作用，如果两个数字之间用 `/` 分隔，Sass 将打印结果为斜杠分隔而不是除法，除非满足以下条件之一：

* 其中一个表达式不是字面量数字。
* 结果存储在变量中或由函数返回。
* 操作被括号包围，除非这些括号位于包含该操作的列表之外。
* 结果作为另一个操作（除 `/` 以外）的一部分使用。
* 结果由 [计算] 返回。

[计算]: /documentation/values/calculations

你可以使用 [`list.slash()`] 来强制 `/` 作为分隔符使用。

[`list.slash()`]: /documentation/modules/list#slash

{% codeExample 'slash-separated-values', false %}
  @use "sass:list";

  @debug 15px / 30px; // 15px/30px
  @debug (10px + 5px) / 30px; // 0.5
  @debug list.slash(10px + 5px, 30px); // 15px/30px

  $result: 15px / 30px;
  @debug $result; // 0.5

  @function fifteen-divided-by-thirty() {
    @return 15px / 30px;
  }
  @debug fifteen-divided-by-thirty(); // 0.5

  @debug (15px/30px); // 0.5
  @debug (bold 15px/30px sans-serif); // bold 15px/30px sans-serif
  @debug 15px/30px + 1; // 1.5
  ===
  @use "sass:list";

  @debug 15px / 30px  // 15px/30px
  @debug (10px + 5px) / 30px  // 0.5
  @debug list.slash(10px + 5px, 30px)  // 15px/30px

  $result: 15px / 30px
  @debug $result  // 0.5

  @function fifteen-divided-by-thirty()
    @return 15px / 30px

  @debug fifteen-divided-by-thirty()  // 0.5

  @debug (15px/30px)  // 0.5
  @debug (bold 15px/30px sans-serif)  // bold 15px/30px sans-serif
  @debug 15px/30px + 1  // 1.5
{% endcodeExample %}

{% render 'doc_snippets/number-units' %}