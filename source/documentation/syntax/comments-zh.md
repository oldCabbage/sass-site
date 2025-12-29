---
title: 注释
introduction: >
  SCSS 和缩进语法中的 Sass 注释工作方式存在很大差异。两种语法都支持两种类型的注释：使用 `/* */` 定义的注释（通常）会被编译成 CSS，以及使用 `//` 定义的注释不会被编译成 CSS。
---

## 在 SCSS 中

SCSS 中的注释与其他语言（如 JavaScript）中的注释工作方式类似。
**单行注释** 以 `//` 开头，直到该行结束。
单行注释中的任何内容都不会被编译成 CSS；从 Sass 的角度来看，它们可以不存在。它们也被称为 **静默注释**，因为它们不会生成任何 CSS。

**多行注释** 以 `/*` 开头，以下一个 `*/` 结束。如果多行注释写在一个 [语句][] 允许的地方，它会被编译成 CSS 注释。与静默注释相对，它们也被称为 **响亮注释**。被编译成 CSS 的多行注释可以包含 [插值][]，这些插值将在注释编译之前进行评估。

默认情况下，多行注释会在 [压缩模式][] 下从编译的 CSS 中移除。但是，如果注释以 `/*!` 开头，则始终会被包含在 CSS 输出中。

[statement]: /documentation/syntax/structure#statements
[interpolation]: /documentation/interpolation
[compressed mode]: /documentation/cli/dart-sass/#style

{% codeExample 'scss-comment', true, 'scss' %}
  // 这个注释不会包含在 CSS 中。

  /* 但这个注释会，除非在压缩模式下。 */

  /* 它也可以包含插值：
  * 1 + 1 = #{1 + 1} */

  /*! 这个注释即使在压缩模式下也会包含。 */

  p /* 多行注释可以写在任何
    * 允许空格的地方。 */ .sans {
    font: Helvetica, // 单行注释也可以。
          sans-serif;
  }
{% endcodeExample %}

## 在 Sass 中

缩进语法中的注释略有不同：它们基于缩进，就像语法的其他部分一样。与 SCSS 一样，使用 `//` 编写的静默注释永远不会被编译成 CSS，但与 SCSS 不同的是，`//` 下面的所有缩进内容也会被注释掉。

以 `/*` 开头的缩进语法注释与缩进方式相同，只是它们会被编译成 CSS。因为注释的范围基于缩进，所以结束的 `*/` 是可选的。与 SCSS 一样，`/*` 注释可以包含 [插值][]，并且可以以 `/*!` 开头以避免在压缩模式下被移除。

在缩进语法中，注释也可以用于 [表达式][] 中。在这种情况下，它们的语法与 SCSS 中的语法完全相同。

[interpolation]: /documentation/interpolation
[expressions]: /documentation/syntax/structure#expressions

{% codeExample 'sass-comment', true, 'sass' %}
  // 这个注释不会包含在 CSS 中。
    这也被注释掉了。

  /* 但这个注释会，除非在压缩模式下。

  /* 它也可以包含插值：
    1 + 1 = #{1 + 1}

  /*! 这个注释即使在压缩模式下也会包含。

  p .sans
    font: Helvetica, /* 内联注释必须关闭。 */ sans-serif
{% endcodeExample %}

## 文档注释

在使用 Sass 编写样式库时，可以使用注释来记录您的库提供的 [mixin][]、[函数][]、[变量][] 和 [占位符选择器][]，以及库本身。这些注释会被 [SassDoc][] 工具读取，该工具使用它们生成漂亮的文档。查看 [Susy 网格引擎][susy] 的文档以了解其实际效果！

[mixins]: /documentation/at-rules/mixin
[functions]: /documentation/at-rules/function
[variables]: /documentation/variables
[placeholder selectors]: /documentation/style-rules/placeholder-selectors
[SassDoc]: http://sassdoc.com
[susy]: http://oddbird.net/susy/docs/index.html

文档注释是静默注释，使用三个斜杠 (`///`) 直接写在要记录的内容上方。SassDoc 将注释中的文本解析为 [Markdown][]，并支持许多有用的 [注解][] 来详细描述它。

[Markdown]: https://www.markdownguide.org/getting-started
[annotations]: http://sassdoc.com/annotations/

{% codeExample 'documentation-comment' %}
  /// Computes an exponent.
  ///
  /// @param {number} $base
  ///   The number to multiply by itself.
  /// @param {integer (unitless)} $exponent
  ///   The number of `$base`s to multiply together.
  /// @return {number} `$base` to the power of `$exponent`.
  @function pow($base, $exponent) {
    $result: 1;
    @for $_ from 1 through $exponent {
      $result: $result * $base;
    }
    @return $result;
  }
  ===
  /// Computes an exponent.
  ///
  /// @param {number} $base
  ///   The number to multiply by itself.
  /// @param {integer (unitless)} $exponent
  ///   The number of `$base`s to multiply together.
  /// @return {number} `$base` to the power of `$exponent`.
  @function pow($base, $exponent)
    $result: 1
    @for $_ from 1 through $exponent
      $result: $result * $base

    @return $result
{% endcodeExample %}