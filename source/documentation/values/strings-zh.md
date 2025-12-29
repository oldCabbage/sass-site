---
title: 字符串
table_of_contents: true
introduction: >
  字符串是由字符（具体来说是 [Unicode 码位](https://en.wikipedia.org/wiki/Code_point)）组成的序列。Sass 支持两种内部结构相同但渲染方式不同的字符串：[带引号的字符串](#quoted)，例如 `"Helvetica Neue"`，和 [不带引号的字符串](#unquoted)（也称为 *标识符*），例如 `bold`。这两者共同涵盖了 CSS 中出现的各种文本。
---

{% funFact %}
  你可以使用 [`string.unquote()` 函数][] 将带引号的字符串转换为不带引号的字符串，使用 [`string.quote()` 函数][] 将不带引号的字符串转换为带引号的字符串。

  [`string.unquote()` 函数]: /documentation/modules/string#unquote
  [`string.quote()` 函数]: /documentation/modules/string#quote

  {% codeExample 'fun-fact-strings', false %}
    @use "sass:string";

    @debug string.unquote(".widget:hover"); // .widget:hover
    @debug string.quote(bold); // "bold"
    ===
    @use "sass:string"

    @debug string.unquote(".widget:hover")  // .widget:hover
    @debug string.quote(bold)  // "bold"
  {% endcodeExample %}
{% endfunFact %}

## 转义字符

所有 Sass 字符串都支持标准 CSS [转义码][]：

[转义码]: https://developer.mozilla.org/en-US/docs/Web/CSS/string#Syntax

* 任何不是从 A 到 F 的字母或从 0 到 9 的数字（甚至是换行符！）的字符都可以通过在其前面写 `\` 来包含在字符串中。

* 任何字符都可以通过写 `\` 后跟其 [Unicode 码位编号][] 的 [十六进制][] 表示来包含在字符串中。你可以在码位编号后面可选地添加一个空格来指示 Unicode 编号的结束。

  [Unicode 码位编号]: https://en.wikipedia.org/wiki/List_of_Unicode_characters
  [十六进制]: https://en.wikipedia.org/wiki/Hexadecimal

{% codeExample 'escapes', false %}
  @debug "\""; // '"'
  @debug \.widget; // \.widget
  @debug "\a"; // "\a" (一个仅包含换行符的字符串)
  @debug "line1\a line2"; // "line1\a line2"
  @debug "Nat + Liz \1F46D"; // "Nat + Liz 👭"
  ===
  @debug "\""  // '"'
  @debug \.widget  // \.widget
  @debug "\a"  // "\a" (一个仅包含换行符的字符串)
  @debug "line1\a line2"  // "line1\a line2"
  @debug "Nat + Liz \1F46D"  // "Nat + Liz 👭"
{% endcodeExample %}

{% funFact %}
  对于允许出现在字符串中的字符，写入 Unicode 转义与写入字符本身会产生完全相同的字符串。
{% endfunFact %}

## 带引号的字符串

带引号的字符串用单引号或双引号括起来，例如 `"Helvetica Neue"`。它们可以包含 [插值][]，以及任何未转义的字符，除了：

[插值]: /documentation/interpolation

* `\`，可以转义为 `\\`；
* `'` 或 `"`，用于定义该字符串的那个引号，可以转义为 `\'` 或 `\"`；
* 换行符，可以转义为 `\a `（包括后面的空格）。

带引号的字符串保证会被编译为与原始 Sass 字符串内容相同的 CSS 字符串。确切的格式可能会根据实现或配置有所不同——包含双引号的字符串可能会被编译为 `"\""` 或 `'"'`，非 [ASCII][] 字符可能会或可能不会被转义。但在任何标准兼容的 CSS 实现中，包括所有浏览器中，都应该被正确解析。

[ASCII]: https://en.wikipedia.org/wiki/ASCII

{% codeExample 'quoted', false %}
  @debug "Helvetica Neue"; // "Helvetica Neue"
  @debug "C:\\Program Files"; // "C:\\Program Files"
  @debug "\"Don't Fear the Reaper\""; // "\"Don't Fear the Reaper\""
  @debug "line1\a line2"; // "line1\a line2"

  $roboto-variant: "Mono";
  @debug "Roboto #{$roboto-variant}"; // "Roboto Mono"
  ===
  @debug "Helvetica Neue"  // "Helvetica Neue"
  @debug "C:\\Program Files"  // "C:\\Program Files"
  @debug "\"Don't Fear the Reaper\""  // "\"Don't Fear the Reaper\""
  @debug "line1\a line2"  // "line1\a line2"

  $roboto-variant: "Mono"
  @debug "Roboto #{$roboto-variant}"  // "Roboto Mono"
{% endcodeExample %}

{% funFact %}
  当带引号的字符串通过插值注入到另一个值时，[它的引号会被移除][]！这使得编写可以注入到样式规则中的选择器字符串变得容易，而不会添加引号。

  [它的引号会被移除]: /documentation/interpolation#quoted-strings
{% endfunFact %}

## 不带引号的字符串

不带引号的字符串写成 CSS [标识符][]，遵循下面的语法图。它们可以在任何地方包含 [插值][]。

[标识符]: https://drafts.csswg.org/css-syntax-3/#ident-token-diagram
[插值]: /documentation/interpolation

<figure>
  <object type="image/svg+xml" data="/assets/img/illustrations/identifier-diagram.svg"></object>
  <figcaption class="copyright">
    铁路图版权 © 2018 W3C<sup>®</sup> (MIT, ERCIM, Keio, Beihang). W3C <a href="http://www.w3.org/Consortium/Legal/ipr-notice#Legal_Disclaimer">责任声明</a>，<a href="http://www.w3.org/Consortium/Legal/ipr-notice#W3C_Trademarks">商标</a> 和 <a href="http://www.w3.org/Consortium/Legal/2015/copyright-software-and-document">许可文档许可</a> 规则适用。
  </figcaption>
</figure>

{% codeExample 'unquoted', false %}
  @debug bold; // bold
  @debug -webkit-flex; // -webkit-flex
  @debug --123; // --123

  $prefix: ms;
  @debug -#{$prefix}-flex; // -ms-flex
  ===
  @debug bold  // bold
  @debug -webkit-flex  // -webkit-flex
  @debug --123  // --123

  $prefix: ms
  @debug -#{$prefix}-flex  // -ms-flex
{% endcodeExample %}

{% headsUp %}
  并非所有标识符都被解析为不带引号的字符串：

  * [CSS 颜色名称][] 被解析为 [颜色][]。
  * `null` 被解析为 [Sass 的 `null` 值][]。
  * `true` 和 `false` 被解析为 [布尔值][]。
  * `not`，`and` 和 `or` 被解析为 [布尔运算符][]。

  [CSS 颜色名称]: https://developer.mozilla.org/en-US/docs/Web/CSS/color_value#Color_keywords
  [颜色]: /documentation/values/colors
  [Sass 的 `null` 值]: /documentation/values/null
  [布尔值]: /documentation/values/booleans
  [布尔运算符]: /documentation/operators/boolean

  因此，通常建议除非你在编写使用不带引号字符串的 CSS 属性值，否则最好写带引号的字符串。
{% endheadsUp %}

### 不带引号字符串中的转义字符

{% compatibility 'dart: "1.11.0"', 'libsass: false', 'ruby: false', 'feature: "Normalization"' %}
  LibSass，Ruby Sass 和 Dart Sass 的旧版本不会对标识符中的转义字符进行规范化。相反，不带引号字符串中的文本就是用户写的精确文本。例如，`\1F46D` 和 `👭` 不被认为是等价的。
{% endcompatibility %}

当解析不带引号的字符串时，转义字符的文本被解析为字符串的一部分。例如，`\a ` 被解析为字符 `\`，`a` 和空格。为了确保 CSS 中含义相同的不带引号字符串被解析为相同的方式，这些转义字符被 *规范化*。对于每个码位，无论是转义的还是未转义的：

* 如果它是一个有效的标识符字符，它会被包含在不带引号的字符串中且未转义。例如，`\1F46D` 返回不带引号的字符串 `👭`。

* 如果它是一个可打印字符但不是换行符或制表符，它会在一个 `\` 后面被包含。例如，`\21 ` 返回不带引号的字符串 `\!`。

* 否则，包含一个尾随空格的小写 Unicode 转义会被包含。例如，`\7Fx` 返回不带引号的字符串 `\7f x`。

{% codeExample 'normalization', false %}
  @use "sass:string";

  @debug \1F46D; // 👭
  @debug \21; // \!
  @debug \7Fx; // \7f x
  @debug string.length(\7Fx); // 5
  ===
  @use "sass:string"

  @debug \1F46D  // 👭
  @debug \21  // \!
  @debug \7Fx  // \7f x
  @debug string.length(\7Fx)  // 5
{% endcodeExample %}

### 其他不带引号的字符串

除了标识符，CSS 语法中还有一些不寻常的部分被解析为不带引号的字符串。这些包括：

* [特殊函数] 如 `url()` 和 `element()`，它们的参数语法与普通 CSS 表达式语法不同。

* [Unicode 范围] 令牌如 `U+0-7F` 或 `U+4??`。

* [哈希令牌]，其值为标识符但不是有效的十六进制颜色。

* 值 `%`。（只有当它不直接位于两个其他值之间时才可用，否则它会与 [取模运算符] 模糊不清。）

* 特殊值 `!important`。

[特殊函数]: /documentation/syntax/special-functions/
[Unicode 范围]: https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/unicode-range
[哈希令牌]: https://www.w3.org/TR/css-syntax-3/#ref-for-typedef-hash-token%E2%91%A0
[取模运算符]: /documentation/operators/numeric/

{% codeExample 'other-unquoted-strings', false %}
  @debug url(https://example.org); // url(https://example.org)
  @debug U+4??; // U+4??
  @debug #my-background; // #my-background
  @debug %; // %
  @debug !important; // !important
  ===
  @debug url(https://example.org)  // url(https://example.org)
  @debug U+4??  // U+4??
  @debug #my-background  // #my-background
  @debug %  // %
  @debug !important  // !important
{% endcodeExample %}

## 字符串索引

Sass 有许多 [字符串函数][] 接受或返回数字，称为 *索引*，它们指代字符串中的字符。索引 1 表示字符串的第一个字符。请注意这与许多编程语言不同，它们的索引从 0 开始！Sass 还使引用字符串的末尾变得容易。索引 -1 表示字符串的最后一个字符，-2 表示倒数第二个字符，以此类推。

[字符串函数]: /documentation/modules/string

{% codeExample 'string-indexes', false %}
  @use "sass:string";

  @debug string.index("Helvetica Neue", "Helvetica"); // 1
  @debug string.index("Helvetica Neue", "Neue"); // 11
  @debug string.slice("Roboto Mono", -4); // "Mono"
  ===
  @use "sass:string"

  @debug string.index("Helvetica Neue", "Helvetica")  // 1
  @debug string.index("Helvetica Neue", "Neue")  // 11
  @debug string.slice("Roboto Mono", -4)  // "Mono"
{% endcodeExample %}