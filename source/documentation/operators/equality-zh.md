---
title: 等性运算符
---

{% compatibility 'dart: true', 'libsass: false', 'ruby: "4.0.0 (unreleased)"', 'feature: "Unitless Equality"'%}
  LibSass 和较早版本的 Ruby Sass 认为没有单位的数字等于具有任何单位的相同数字。由于这种行为违反了 [传递性][], 因此已被弃用，并从最近的版本中移除。

  [transitivity]: https://en.wikipedia.org/wiki/Transitive_relation
{% endcompatibility %}

等性运算符返回两个值是否相同。它们的语法为 `<expression> == <expression>`，用于判断两个 [表达式][] 是否相等，以及 `<expression> != <expression>`，用于判断两个表达式是否 *不* 相等。两个值被认为是相等的，如果它们是相同类型 *且* 具有相同的值，这取决于不同的类型意味着不同的东西：

[expressions]: /documentation/syntax/structure#expressions

* [数字][] 在具有相同的值 *且* 相同的单位时被认为是相等的，或者在它们的单位相互转换后值相等时被认为是相等的。
* [字符串][] 的一个不寻常之处在于，具有相同内容的 [未引用][] 和 [引用][] 字符串被认为是相等的。
* [颜色][] 在处于相同的 [颜色空间][] 且具有相同的通道值时被认为是相等的，*或者* 如果它们都处于 [旧颜色空间][] 且具有相同的 RGBA 通道值，则被认为是相等的。
* [列表][] 在其内容相等时被认为是相等的。逗号分隔的列表不等于空格分隔的列表，带括号的列表不等于不带括号的列表。
* [映射][] 在它们的键和值都相等时被认为是相等的。
* [计算][] 在它们的名称和参数都相等时被认为是相等的。操作参数是按文本进行比较的。
* [`true`, `false`][] 和 [`null`][] 仅等于它们自身。
* [函数][] 等于相同的函数。函数是按 *引用* 进行比较的，因此即使两个函数具有相同的名称和定义，如果它们不是在相同的位置定义的，也会被认为是不同的。

[Numbers]: /documentation/values/numbers
[Strings]: /documentation/values/strings
[quoted]: /documentation/values/strings#quoted
[unquoted]: /documentation/values/strings#unquoted
[Colors]: /documentation/values/colors
[color space]: /documentation/values/colors#color-spaces
[legacy color spaces]: /documentation/values/colors#legacy-color-spaces
[Lists]: /documentation/values/lists
[`true`, `false`]: /documentation/values/booleans
[`null`]: /documentation/values/null
[Maps]: /documentation/values/maps
[Calculations]: /documentation/values/calculations
[Functions]: /documentation/values/functions

{% codeExample 'equality', false %}
  @debug 1px == 1px; // true
  @debug 1px != 1em; // true
  @debug 1 != 1px; // true
  @debug 96px == 1in; // true

  @debug "Helvetica" == Helvetica; // true
  @debug "Helvetica" != "Arial"; // true

  @debug hsl(34, 35%, 92.1%) == #f2ece4; // true
  @debug rgba(179, 115, 153, 0.5) != rgba(179, 115, 153, 0.8); // true

  @debug (5px 7px 10px) == (5px 7px 10px); // true
  @debug (5px 7px 10px) != (10px 14px 20px); // true
  @debug (5px 7px 10px) != (5px, 7px, 10px); // true
  @debug (5px 7px 10px) != [5px 7px 10px]; // true

  $theme: ("venus": #998099, "nebula": #d2e1dd);
  @debug $theme == ("venus": #998099, "nebula": #d2e1dd); // true
  @debug $theme != ("venus": #998099, "iron": #dadbdf); // true

  @debug true == true; // true
  @debug true != false; // true
  @debug null != false; // true

  @debug get-function("rgba") == get-function("rgba"); // true
  @debug get-function("rgba") != get-function("hsla"); // true
  ===
  @debug 1px == 1px  // true
  @debug 1px != 1em  // true
  @debug 1 != 1px  // true
  @debug 96px == 1in  // true

  @debug "Helvetica" == Helvetica  // true
  @debug "Helvetica" != "Arial"  // true

  @debug hsl(34, 35%, 92.1%) == #f2ece4  // true
  @debug rgba(179, 115, 153, 0.5) != rgba(179, 115, 153, 0.8)  // true

  @debug (5px 7px 10px) == (5px 7px 10px)  // true
  @debug (5px 7px 10px) != (10px 14px 20px)  // true
  @debug (5px 7px 10px) != (5px, 7px, 10px)  // true
  @debug (5px 7px 10px) != [5px 7px 10px]  // true

  $theme: ("venus": #998099, "nebula": #d2e1dd)
  @debug $theme == ("venus": #998099, "nebula": #d2e1dd)  // true
  @debug $theme != ("venus": #998099, "iron": #dadbdf)  // true

  @debug calc(10px + 10%) == calc(10px + 10%)  // true
  @debug calc(10% + 10px) == calc(10px + 10%)  // false

  @debug true == true  // true
  @debug true != false  // true
  @debug null != false  // true

  @debug get-function("rgba") == get-function("rgba")  // true
  @debug get-function("rgba") != get-function("hsla")  // true
{% endcodeExample %}