---
title: "@import"
table_of_contents: true
introduction: >
  Sass 扩展了 CSS 的 [`@import`
  规则](https://developer.mozilla.org/en-US/docs/Web/CSS/@import)，使其能够导入 Sass 和 CSS 样式表，并提供对
  [mixin](/documentation/at-rules/mixin)、
  [函数](/documentation/at-rules/function) 和
  [变量](/documentation/variables) 的访问，并将多个样式表的 CSS 结合在一起。与普通的 CSS 导入不同，后者在渲染页面时需要浏览器发出多个 HTTP 请求，Sass 导入完全在编译期间处理。
---

Sass 导入的语法与 CSS 导入相同，不同之处在于它们允许使用逗号分隔多个导入，而不是为每个导入单独使用 `@import`。另外，在 [缩进语法][], 导入的 URL 不需要使用引号。

[indented syntax]: /documentation/syntax#the-indented-syntax

{% headsUp %}
  从 Dart Sass 1.80.0 开始，`@import` 规则已被 [弃用]，将在 Dart Sass 3.0.0 中从语言中移除。建议使用 [`@use` 规则] 代替。

  [deprecated]: /documentation/breaking-changes/import
  [`@use` 规则]: /documentation/at-rules/use

  <h4>{{ "为什么 `@import` 有问题？" | markdown }}</h4>

  `@import` 规则存在一些严重问题：

  * `@import` 使所有变量、mixin 和函数全局可访问。这使得人们（或工具）很难确定任何东西是在哪里定义的。

  * 由于一切都是全局的，库必须为其所有成员添加前缀以避免命名冲突。

  * [`@extend` 规则][] 也是全局的，这使得很难预测哪些样式规则会被扩展。

    [`@extend` 规则]: /documentation/at-rules/extend

  * 每次 `@import` 文件时，该文件都会被执行并发出其 CSS，这增加了编译时间并产生了冗长的输出。

  * 没有定义私有成员或不可访问下游样式表的占位符选择器的方法。

  新的模块系统和 `@use` 规则解决了所有这些问题。

  <h4>{{ '如何迁移？' | markdown }}</h4>

  我们编写了一个 [迁移工具][]，可以自动将大多数 `@import` 基础的代码转换为 `@use` 基础的代码。只需将其指向您的入口点并运行即可！

  [migration tool]: /documentation/cli/migrator
{% endheadsUp %}

{% codeExample 'import' %}
  // foundation/_code.scss
  code {
    padding: .25em;
    line-height: 0;
  }
  ---
  // foundation/_lists.scss
  ul, ol {
    text-align: left;

    & & {
      padding: {
        bottom: 0;
        left: 0;
      }
    }
  }
  ---
  // style.scss
  @import 'foundation/code', 'foundation/lists';
  ===
  // foundation/_code.sass
  code
    padding: .25em
    line-height: 0
  ---
  // foundation/_lists.sass
  ul, ol
    text-align: left

    & &
      padding:
        bottom: 0
        left: 0
  ---
  // style.sass
  @import foundation/code, foundation/lists
  ===
  code {
    padding: .25em;
    line-height: 0;
  }

  ul, ol {
    text-align: left;
  }
  ul ul, ol ol {
    padding-bottom: 0;
    padding-left: 0;
  }
{% endcodeExample %}

当 Sass 导入一个文件时，该文件的内容被视为直接出现在 `@import` 的位置。导入文件中的任何 [mixin][], [函数][] 和 [变量][] 都会被提供，并且其所有的 CSS 都会在 `@import` 写入的确切位置包含。此外，在 `@import` 之前定义的任何 mixin、函数或变量（包括来自其他 `@import` 的）在导入的样式表中都是可用的。

[mixins]: /documentation/at-rules/mixin
[functions]: /documentation/at-rules/function
[variables]: /documentation/variables

{% headsUp %}
  如果同一个样式表被导入多次，它会在每次导入时重新评估。如果它只定义了函数和 mixin，这通常不是什么大问题，但如果它包含样式规则，它们会被编译为 CSS 多次。
{% endheadsUp %}

## 查找文件

为每个导入的样式表写出绝对 URL 并不好玩，因此 Sass 的文件查找算法使其变得简单一些。首先，您不需要显式写出要导入的文件的扩展名；`@import "variables"` 将会自动加载 `variables.scss`、`variables.sass` 或 `variables.css`。

{% headsUp %}
  为了确保样式表在每个操作系统上都能正常工作，Sass 通过 *URL* 而不是 *文件路径* 导入文件。这意味着即使在 Windows 上，您也需要使用正斜杠，而不是反斜杠。
{% endheadsUp %}

### 加载路径

所有 Sass 实现都允许用户提供 *加载路径*：文件系统中的路径，Sass 在解析导入时会查找这些路径。例如，如果您传递 `node_modules/susy/sass` 作为加载路径，您可以使用 `@import "susy"` 来加载 `node_modules/susy/sass/susy.scss`。

导入始终会首先相对于当前文件进行解析。如果没有任何相对文件匹配导入，才会使用加载路径。这确保了您在添加新库时不会意外破坏相对导入。

{% funFact %}
  与某些其他语言不同，Sass 不要求您为相对导入使用 `./`。相对导入始终可用。
{% endfunFact %}

### 部分文件

作为一种约定，仅用于导入而不会单独编译的 Sass 文件以 `_` 开头（例如 `_code.scss`）。这些文件被称为 *部分文件*，它们告诉 Sass 工具不要尝试单独编译这些文件。在导入部分文件时可以省略 `_`。

### 索引文件

{% compatibility 'dart: true', 'libsass: "3.6.0"', 'ruby: "3.6.0"' %}{% endcompatibility %}

如果您在一个文件夹中编写了 `_index.scss` 或 `_index.sass`，当该文件夹本身被导入时，会加载该文件。

{% codeExample 'index-files' %}
  // foundation/_code.scss
  code {
    padding: .25em;
    line-height: 0;
  }
  ---
  // foundation/_lists.scss
  ul, ol {
    text-align: left;

    & & {
      padding: {
        bottom: 0;
        left: 0;
      }
    }
  }
  ---
  // foundation/_index.scss
  @import 'code', 'lists';
  ---
  // style.scss
  @import 'foundation';
  ===
  // foundation/_code.sass
  code
    padding: .25em
    line-height: 0
  ---
  // foundation/_lists.sass
  ul, ol
    text-align: left

    & &
      padding:
        bottom: 0
        left: 0
  ---
  // foundation/_index.sass
  @import code, lists
  ---
  // style.sass
  @import foundation
  ===
  code {
    padding: .25em;
    line-height: 0;
  }

  ul, ol {
    text-align: left;
  }
  ul ul, ol ol {
    padding-bottom: 0;
    padding-left: 0;
  }
{% endcodeExample %}

### 自定义导入器

所有 Sass 实现都提供了一种定义自定义导入器的方法，这些导入器控制 `@import` 如何定位样式表：

* [Node Sass][] 和 [Dart Sass on npm][] 提供了 JS API 的一部分 [`importer` 选项][]。

* [Dart Sass on pub][] 提供了一个抽象的 [`Importer` 类][]，可以被自定义导入器扩展。

* [Ruby Sass][] 提供了一个抽象的 [`Importers::Base` 类][]，可以被自定义导入器扩展。

[Node Sass]: https://npmjs.com/package/node-sass
[Dart Sass on npm]: https://npmjs.com/package/sass
[`importer` 选项]: https://github.com/sass/node-sass#importer--v200---experimental
[Dart Sass on pub]: https://pub.dartlang.org/packages/sass
[`Importer` 类]: https://pub.dartlang.org/documentation/sass/latest/sass/Importer-class.html
[Ruby Sass]: /ruby-sass
[`Importers::Base` 类]: https://www.rubydoc.info/gems/sass/Sass/Importers/Base

## 嵌套

导入通常写在样式表的顶层，但并不一定。它们也可以嵌套在 [样式规则][] 或 [纯 CSS 规则][] 中。导入的 CSS 在该上下文中嵌套，这使得嵌套导入对于将一段 CSS 作用域限定到特定元素或媒体查询非常有用。嵌套导入中定义的顶层 [mixin][], [函数][] 和 [变量][] 只在嵌套上下文中可用。

[样式规则]: /documentation/style-rules
[纯 CSS 规则]: /documentation/at-rules/css
[mixin]: /documentation/at-rules/mixin
[函数]: /documentation/at-rules/function
[变量]: /documentation/variables

{% codeExample 'nesting' %}
  // _theme.scss
  pre, code {
    font-family: 'Source Code Pro', Helvetica, Arial;
    border-radius: 4px;
  }
  ---
  // style.scss
  .theme-sample {
    @import "theme";
  }
  ===
  // _theme.sass
  pre, code
    font-family: 'Source Code Pro', Helvetica, Arial
    border-radius: 4px
  ---
  // style.sass
  .theme-sample
    @import theme
  ===
  .theme-sample pre, .theme-sample code {
    font-family: 'Source Code Pro', Helvetica, Arial;
    border-radius: 4px;
  }
{% endcodeExample %}

{% funFact %}
  嵌套导入对于作用域第三方样式表非常有用，但如果您是导入的样式表的作者，通常更好的方法是将样式写在一个 [mixin][] 中，并在嵌套上下文中包含该 mixin。mixin 可以以更灵活的方式使用，并且在查看导入的样式表时可以更清楚地了解其使用方式。

  [mixin]: /documentation/at-rules/mixin
{% endfunFact %}

{% headsUp %}
  嵌套导入中的 CSS 被评估为 mixin，这意味着任何 [父选择器][] 将引用嵌套样式表的选择器。

  [父选择器]: /documentation/style-rules/parent-selector

  {% codeExample 'parent-selector' %}
    // _theme.scss
    ul li {
      $padding: 16px;
      padding-left: $padding;
      [dir=rtl] & {
        padding: {
          left: 0;
          right: $padding;
        }
      }
    }
    ---
    // style.scss
    .theme-sample {
      @import "theme";
    }
    ===
    // _theme.sass
    ul li
      $padding: 16px
      padding-left: $padding
      [dir=rtl] &
        padding:
          left: 0
          right: $padding
    ---
    // style.sass
    .theme-sample
      @import theme
    ===
    .theme-sample ul li {
      padding-left: 16px;
    }
    [dir=rtl] .theme-sample ul li {
      padding-left: 0;
      padding-right: 16px;
    }
  {% endcodeExample %}
{% endheadsUp %}

## 导入 CSS

{% compatibility 'dart: "1.11.0"', 'libsass: "partial"', 'ruby: false' %}
  LibSass 支持导入扩展名为 `.css` 的文件，但与规范相反，它们被视为 SCSS 文件而不是被解析为 CSS。这种行为已被弃用，正在开发中的更新将支持下面描述的行为。
{% endcompatibility %}

除了导入 `.sass` 和 `.scss` 文件外，Sass 还可以导入普通的 `.css` 文件。唯一的规则是导入 *不能* 显式包含 `.css` 扩展名，因为这用于指示 [纯 CSS `@import`][]。

[纯 CSS `@import`]: #plain-css-imports

{% codeExample 'import-css' %}
  // code.css
  code {
    padding: .25em;
    line-height: 0;
  }
  ---
  // style.scss
  @import 'code';
  ===
  // code.css
  code {
    padding: .25em;
    line-height: 0;
  }
  ---
  // style.sass
  @import code
  ===
  code {
    padding: .25em;
    line-height: 0;
  }
{% endcodeExample %}

Sass 导入的 CSS 文件不允许使用任何特殊的 Sass 特性。为了确保作者不会在 CSS 中意外编写 Sass，所有不是有效 CSS 的 Sass 特性都会产生错误。否则，CSS 将按原样渲染。甚至可以 [扩展][]！

[扩展]: /documentation/at-rules/extend

## 纯 CSS `@import` 语句

{% compatibility 'dart: true', 'libsass: "partial"', 'ruby: true' %}
  默认情况下，LibSass 正确处理纯 CSS 导入。但是，任何 [自定义导入器][] 将错误地应用于纯 CSS `@import` 规则，使得这些规则可能加载 Sass 文件。

  [自定义导入器]: /documentation/js-api/interfaces/LegacySharedOptions#importer
{% endcompatibility %}

因为 `@import` 在 CSS 中也有定义，Sass 需要一种方法在不尝试在编译时导入文件的情况下编译纯 CSS `@import` 语句。为此，并确保 SCSS 尽可能成为 CSS 的超集，Sass 会将具有以下特征的任何 `@import` 编译为纯 CSS 导入：

* URL 以 `.css` 结尾的导入。
* URL 以 `http://` 或 `https://` 开头的导入。
* URL 以 `url()` 写作的导入。
* 包含媒体查询的导入。

{% codeExample 'plain-css-imports' %}
  @import "theme.css";
  @import "http://fonts.googleapis.com/css?family=Droid+Sans";
  @import url(theme);
  @import "landscape" screen and (orientation: landscape);
  ===
  @import "theme.css"
  @import "http://fonts.googleapis.com/css?family=Droid+Sans"
  @import url(theme)
  @import "landscape" screen and (orientation: landscape)
{% endcodeExample %}

### 插值

虽然 Sass 导入不能使用 [插值][]（以确保总是可以确定 [mixin][], [函数][] 和 [变量][] 的来源），但纯 CSS 导入可以。这使得可以动态生成导入，例如基于 mixin 参数。

[插值]: /documentation/interpolation
[mixin]: /documentation/at-rules/mixin
[函数]: /documentation/at-rules/function
[变量]: /documentation/variables

{% codeExample 'interpolation' %}
  @mixin google-font($family) {
    @import url("http://fonts.googleapis.com/css?family=#{$family}");
  }

  @include google-font("Droid Sans");
  ===
  @mixin google-font($family)
    @import url("http://fonts.googleapis.com/css?family=#{$family}")


  @include google-font("Droid Sans")
{% endcodeExample %}

## 导入和模块

{% render 'doc_snippets/module-system-status' %}

Sass 的 [模块系统][] 与 `@import` 完美集成，无论您是导入包含 `@use` 规则的文件，还是将包含导入的文件作为模块加载。我们希望使从 `@import` 过渡到 `@use` 尽可能平滑。

[模块系统]: /documentation/at-rules/use

### 导入模块系统文件

当您导入包含 `@use` 规则的文件时，导入文件可以访问该文件中直接定义的所有成员（包括私有成员），但*不能*访问该文件加载的模块中的任何成员。但是，如果该文件包含 [`@forward` 规则][], 导入文件将可以访问转发的成员。这意味着您可以导入一个为模块系统编写的设计库。

[`@forward` 规则]: /documentation/at-rules/forward

{% headsUp %}
  当导入包含 `@use` 规则的文件时，由这些规则传递加载的所有 CSS 都会被包含在生成的样式表中，即使它已经被另一个导入包含。如果不小心，这可能会导致膨胀的 CSS 输出！
{% endheadsUp %}

#### 仅导入文件

对于 `@use` 可能有意义的 API 对于 `@import` 可能没有意义。例如，`@use` 默认为所有成员添加命名空间，因此可以安全地使用短名称，但 `@import` 不会，因此您可能需要更长的名称。如果您是库作者，您可能担心如果将库更新为使用新的模块系统，现有的 `@import` 用户会中断。

为了使这更容易，Sass 还支持 *仅导入文件*。如果您将文件命名为 `<name>.import.scss`，它将仅用于导入，而不是用于 `@use`。这样，您可以保留对 `@import` 用户的兼容性，同时为新的模块系统用户提供良好的 API。

{% codeExample 'import-only-files', false %}
  // _reset.scss

  // 模块系统用户写 `@include reset.list()`。
  @mixin list() {
    ul {
      margin: 0;
      padding: 0;
      list-style: none;
    }
  }
  ---
  // _reset.import.scss

  // 传统导入用户可以继续写 `@include reset-list()`。
  @forward "reset" as reset-*;
  ===
  // _reset.sass

  // 模块系统用户写 `@include reset.list()`。
  @mixin list()
    ul
      margin: 0
      padding: 0
      list-style: none
  ---
  // _