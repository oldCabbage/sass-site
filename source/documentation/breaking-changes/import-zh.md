---
title: '重大变更: @import 和全局内置函数'
introduction: >
  最初，Sass 使用 `@import` 规则通过单个全局命名空间加载其他文件，并且所有内置函数也全局可用。现在我们已经弃用了 Sass 的 `@import` 规则和全局内置函数，因为模块系统（`@use` 和 `@forward` 规则）已经使用了几年。
---

`@import` 导致了许多问题，需要手动对 Sass 成员进行命名空间以避免冲突，当同一个文件被多次导入时会减慢编译速度，并且很难确定给定的变量、mixin 或函数来自何处，无论是对人类还是工具都是如此。

模块系统解决了这些问题，并将 Sass 的模块化提升到与其他现代语言的最佳实践相同水平，但只要 `@import` 仍在语言中，我们就无法获得其全部好处。

自 Dart Sass 1.80.0 起，`@import` 已被弃用。此外，我们还弃用了在 `sass:` 模块中可用的 Sass 内置函数的全局版本。

## 过渡期

{% compatibility 'dart: "1.80.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

Sass `@import` 规则和全局内置函数调用现在会发出弃用警告。虽然 Dart Sass 2.0.0 将很快发布，其中包含各种较小的破坏性更改，但我们预计直到 Dart Sass 3.0.0 才会移除 Sass `@import` 规则和全局内置函数，而 Dart Sass 3.0.0 将在 Dart Sass 1.80.0 发布至少两年后发布。

最终，所有 `@import` 规则将被视为 [纯 CSS `@import`]，可能在中间阶段会有一个任何曾经是 Sass `@import` 的内容都会抛出错误的时期。

[纯 CSS `@import`]: /documentation/at-rules/import/#plain-css-imports

## 自动迁移

你可以使用 [Sass 迁移工具][] 自动更新样式表以使用模块系统。

[Sass 迁移工具]: https://github.com/sass/migrator#readme

```shellsession
$ npm install -g sass-migrator
$ sass-migrator module --migrate-deps your-entrypoint.scss
```

如果你想迁移到全局内置函数，但尚未准备好完全迁移你的 `@import` 规则，可以传递 `--built-in-only` 标志来迁移函数同时保留 `@import` 规则。

## 迁移食谱

### 嵌套导入

虽然 `@import` 可以在 CSS 规则内使用，但 `@use` 必须写在文件的顶层（这是因为每个 `@use` 的模块的 CSS 只会被包含在输出中一次，因此允许它同时在嵌套上下文中和顶层中是没有意义的）。将嵌套的 `@import` 迁移到模块系统有两种方法：

1. 推荐的方法需要一些前期努力，即使用 [mixin] 将你的嵌套模块发出的所有 CSS 包装起来，并在嵌套上下文中包含这些 mixin。这符合大多数其他编程语言的工作方式，即每个文件定义一个函数或类，该函数或类由使用它的文件调用，并且可以非常清楚地知道你希望如何使用该文件。它还使得添加配置更加容易，因为你只需将参数或甚至是 [`@content` 块] 传递给 mixin。

2. 更直接的翻译是使用 [`meta.load-css()` mixin] 直接加载模块的 CSS 到你想要使用的地方。这在你无法控制要加载的文件以创建 mixin 包装时是合适的。请注意，`meta.load-css()` 在进行任何嵌套之前会完全编译 CSS，因此任何 [父选择器] 不会“看到”`meta.load-css()` 调用之外的规则。

[mixin]: /documentation/at-rules/mixin/
[`@content` 块]: /documentation/at-rules/mixin/#content-blocks
[`meta.load-css()` mixin]: /documentation/modules/meta/#load-css
[父选择器]: /documentation/style-rules/parent-selector/

### 配置化主题

人们有时使用 `@import` 的一个模式是拥有一个包含许多部分的组件库，这些部分使用相同的变量而无需显式加载它们，然后有几个不同的“主题”入口点定义这些变量的不同值以提供不同的视觉主题。它们可以直接定义变量，或覆盖基主题部分的默认值。以下是一个简化的示例：

{% codeExample 'import-theme' %}
  // components/_button.scss
  button {
    color: $text-color;
    background-color: $background-color;
  }
  ---
  // _theme.scss
  $text-color: black !default;
  $background-color: white !default;
  ---
  // dark.scss
  $text-color: white;
  $background-color: black;
  @import "theme";

  @import "components/button";
  // 通常这里会导入更多组件。
  ===
  // components/_button.scss
  button
    color: $text-color
    background-color: $background-color
  ---
  // _theme.scss
  $text-color: black
  $background-color: white
  ---
  // dark.scss
  $text-color: white
  $background-color: black
  @import "theme"

  @import "components/button"
  // 通常这里会导入更多组件。
  ===
  button {
    color: white;
    background-color: black;
  }
{% endcodeExample %}

在模块系统中，组件部分需要显式引用它们所引用的变量。但这并不意味着这种类型的主题化不起作用！因为多次 `@use` 同一个模块总是使用相同的配置，因此你可以在入口点中配置一次，所有其他使用都会看到该配置：

{% render 'code_snippets/example-use-theme' %}

{% render 'silencing_deprecations' %}

注意：虽然 `@import` 和全局内置函数的弃用同时发布，我们预计这两个功能也会同时被移除（在 Dart Sass 3.0.0 中），但在 API 的目的上，它们被视为独立的弃用。如果你想同时抑制 `@import` 弃用警告和全局内置函数弃用警告，你需要将 `import` 和 `global-builtin` 传递给 `--silence-deprecation`/`silenceDeprecations`。
