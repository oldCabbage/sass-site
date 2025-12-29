---
title: 解析样式表
introduction: >
  Sass 样式表是从 Unicode 码点序列解析的。它
  是直接解析的，无需先转换为标记流。
---

## 输入编码

{% compatibility 'dart: false', 'libsass: true', 'ruby: true' %}
  Dart Sass 目前 *仅* 支持 UTF-8 编码。因此，最好将所有 Sass 样式表编码为 UTF-8。
{% endcompatibility %}

通常情况下，文档最初只以字节序列的形式提供，必须解码为 Unicode。Sass 按照以下方式执行此解码：

* 如果字节序列以 UTF-8 或 UTF-16 编码的 U+FEFF 字节顺序标记（BYTE ORDER MARK）开头，则使用相应的编码。

* 如果字节序列以纯 ASCII 字符串 `@charset` 开头，Sass 使用 CSS 算法的步骤 2 来确定回退编码
  [确定回退编码][]。

  [determining the fallback encoding]: https://drafts.csswg.org/css-syntax-3/#input-byte-stream

* 否则，使用 UTF-8。

## 解析错误

当 Sass 在样式表中遇到无效语法时，解析将失败，并向用户显示错误信息，包括无效语法的位置和无效的原因。

请注意，这与 CSS 不同，CSS 指定了如何从大多数错误中恢复而不是立即失败。这是 SCSS 不是 *严格* CSS 的超集的少数情况之一。然而，对于 Sass 用户来说，立即看到错误比将它们传递到 CSS 输出更有用。

解析错误的位置可以通过特定于实现的 API 访问。例如，在 Dart Sass 中，您可以访问 [`SassException.span`][]，在 Node Sass 和 Dart Sass 的 JS API 中，您可以访问 [`file`, `line`, 和 `column`][js error] 属性。

[`SassException.span`]: https://pub.dartlang.org/documentation/sass/latest/sass/SassException/span.html
[js error]: https://github.com/sass/node-sass#error-object