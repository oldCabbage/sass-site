---
title: Breaking Changes
introduction: >
  Sass的新版本尽可能保持向后兼容，但有时必须进行重大更改。Sass需要跟上不断发展的CSS规范，并且偶尔需要修复旧语言设计中的错误。
---

在每个重大更改发布之前，Sass实现将为行为将发生变化的样式表生成弃用警告。如果可能，这些警告将包括如何更新已弃用样式以使其向前兼容的建议。

不同的实现对重大更改和弃用有不同的政策。[Dart Sass][]将在发布重大更改前至少三个月发出弃用警告，并且将重大更改与新的主要版本号一起发布**除非该更改对于CSS兼容性是必要的**。CSS兼容性更改通常是非破坏性的且具有时间敏感性，因此可能会与新的次要版本号一起发布。

[Dart Sass]: /dart-sass

这些重大更改即将发布或最近已发布：

* [legacy `if()` function](/documentation/breaking-changes/if-function/)
  从 Dart Sass 1.95.0 开始

* [Private variables in `with`](/documentation/breaking-changes/with-private/)
  从 Dart Sass 1.92.0 开始

* [Misplaced rest arguments](/documentation/breaking-changes/misplaced-rest/)
  从 Dart Sass 1.91.0 开始

* [`type()` function](/documentation/breaking-changes/type-function/) 从 Dart
  Sass 1.86.0 开始

* [`@import`](/documentation/breaking-changes/import/) 从 Dart
  Sass 1.80.0 开始

* [legacy JS API](/documentation/breaking-changes/legacy-js-api/) 从
  Dart Sass 1.79.0 开始。

* [Certain uses of the JS color API are
  deprecated](/documentation/breaking-changes/color-4-api/) 从 Dart
  Sass 1.79.0 开始。

* [A number of color functions are
  deprecated](/documentation/breaking-changes/color-functions/) 从
  Dart Sass 1.79.0 开始，以支持新的CSS Color 4兼容函数。

* [The `meta.feature-exists()` function is
  deprecated](/documentation/breaking-changes/feature-exists/) 从 Dart
  Sass 1.78.0 开始。

* [Mixing declarations with nested rules is changing
  behavior](/documentation/breaking-changes/mixed-decls/) 从 Dart Sass
  1.77.7 开始。

* [Functions and Mixins Beginning with `--` are
  deprecated](/documentation/breaking-changes/css-function-mixin/) 从 Dart
  Sass 1.76.0 开始。

* [Passing a percentage unit to the global `abs()` is
  deprecated](/documentation/breaking-changes/abs-percent/) 从 Dart
  Sass 1.65.0 开始。

* [Passing `null` as an alpha channel to `new SassColor()` is changing
  behavior](/documentation/breaking-changes/null-alpha) 从 Dart
  Sass 1.64.3 开始。

* [Loading Sass as a default export in JS is no longer
  allowed](/documentation/breaking-changes/default-export) 从 Dart
  Sass 1.63.0 开始。

* [A variable may only have a single `!global` or `!default`
  flag](/documentation/breaking-changes/duplicate-var-flags) 从 Dart
  Sass 1.62.0 开始。

* [Strict unary operators](/documentation/breaking-changes/strict-unary/)
  从 Dart Sass 1.55.0 开始。

* [Media Queries Level 4](/documentation/breaking-changes/media-logic/) 从 Dart
  Sass 1.54.0 开始，并在 Dart Sass 1.56.0 结束弃用期。

* [Selectors with invalid combinators are
  invalid](/documentation/breaking-changes/bogus-combinators) 从 Dart
  Sass 1.54.0 开始。

* [`/` is changing from a division operation to a list
  separator](/documentation/breaking-changes/slash-div) 从 Dart Sass
  1.33.0 开始。

* [Functions are stricter about which units they
  allow](/documentation/breaking-changes/function-units) 从 Dart Sass
  1.32.0 开始。

* [Parsing the special syntax of `@-moz-document` will be
  invalid](/documentation/breaking-changes/moz-document) 从 Dart Sass
  1.7.2 开始。

* [Compound selectors could not be
  extended](/documentation/breaking-changes/extend-compound) 在 Dart Sass 1.0.0
  和 Ruby Sass 4.0.0 中。

* [The syntax for CSS custom property values
  changed](/documentation/breaking-changes/css-vars) 在 Dart Sass 1.0.0, LibSass
  3.5.0, 和 Ruby Sass 3.5.0 中。

## Early Opt-In

Dart Sass 用户可以使用 [`--fatal-deprecation` command line
option](/documentation/cli/dart-sass/#fatal-deprecation) 提前将弃用视为错误。