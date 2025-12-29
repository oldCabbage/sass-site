---
title: '文档'
introduction: >
  Sass 是一种编译为 CSS 的样式表语言。它允许您使用
  [变量](/documentation/variables)，[嵌套
  规则](/documentation/style-rules#nesting)，
  [混合宏](/documentation/at-rules/mixin)，
  [函数](/documentation/modules) 等，所有这些都具有完全兼容 CSS 的语法。Sass 帮助保持大型样式表的井井有条，并使在项目内部和跨项目之间共享设计变得容易。
---

- 如果您正在寻找 Sass 的入门指南，请查看 [教程](/guide)。

- 如果您要查找内置的 Sass 函数，请参考 [内置模块参考](/documentation/modules)。

- 如果您从 JavaScript 调用 Sass，您可能需要 [JS API 文档][js]。

- 或者如果您从 Dart 调用 Sass，可以查看 [Dart API 文档][dart]。

- 否则，请使用语言参考的目录！

[js]: https://github.com/sass/node-sass#usage
[dart]: https://pub.dartlang.org/documentation/sass/latest/sass/sass-library.html

## 较旧版本

此文档是为 Sass 语言的最新版本编写的。
如果您使用的是 [Dart Sass] {{ releases['dart-sass'].version }}，您将能够访问此处描述的所有功能。但是，如果您使用的是 Dart Sass 的旧版本或已弃用的 Sass 实现，如 [LibSass] 或 [Ruby Sass]，可能会有一些行为差异。

[Dart Sass]: /dart-sass
[LibSass]: /libsass
[Ruby Sass]: /ruby-sass

在版本或实现之间行为不同的任何地方，文档中都会包含一个兼容性指示器，如下所示：

{% compatibility 'dart: true', 'libsass: "3.6.0"', 'ruby: false', 'feature: "Feature Name"' %}{% endcompatibility %}

带有 "✓" 的实现完全支持相关功能，带有 "✗" 的实现不支持该功能。带有版本号的实现从该版本开始支持相关功能。实现也可以标记为 "partial"：

{% compatibility 'dart: true', 'libsass: "partial"', 'ruby: false' %}
  附加详细信息放在这里。
{% endcompatibility %}

这表示该实现仅支持该功能的一些方面。这些兼容性指示器（以及其他许多指示器）都有一个 "➤" 按钮，可以点击以显示有关实现之间如何不同的更多详细信息，以及哪些版本支持该功能的哪些方面。