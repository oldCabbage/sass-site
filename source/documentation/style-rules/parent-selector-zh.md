---
title: 父选择器
introduction: >
  父选择器 `&` 是 Sass 发明的一种特殊选择器，用于在 [嵌套选择器](/documentation/style-rules#nesting) 中引用外部选择器。它使得可以在更复杂的方式中重用外部选择器，例如添加 [伪类](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes) 或在父选择器 *之前* 添加选择器。
---

当父选择器在内部选择器中使用时，它会被替换为相应外部选择器。这发生在正常嵌套行为之前。

{% codeExample 'parent-selector' %}
  .alert {
    // 父选择器可以用来为外部选择器添加伪类。
    &:hover {
      font-weight: bold;
    }

    // 它也可以用来在特定上下文中为外部选择器添加样式，例如设置为使用从右到左语言的 body。
    [dir=rtl] & {
      margin-left: 0;
      margin-right: 10px;
    }

    // 甚至可以用作伪类选择器的参数。
    :not(&) {
      opacity: 0.8;
    }
  }
  ===
  .alert
    // 父选择器可以用来为外部选择器添加伪类。
    &:hover
      font-weight: bold


    // 它也可以用来在特定上下文中为外部选择器添加样式，例如设置为使用从右到左语言的 body。
    [dir=rtl] &
      margin-left: 0
      margin-right: 10px


    // 甚至可以用作伪类选择器的参数。
    :not(&)
      opacity: 0.8
{% endcodeExample %}

{% headsUp %}
  因为父选择器可能会被类型选择器如 `h1` 替换，因此它只允许出现在复合选择器的开头，就像类型选择器允许的位置一样。例如，`span&` 是不允许的。

  我们正在考虑放宽这一限制。如果您希望帮助实现这一点，请查看 [这个 GitHub 问题][]。

  [这个 GitHub 问题]: https://github.com/sass/sass/issues/1425
{% endheadsUp %}

## 添加后缀

您还可以使用父选择器为外部选择器添加额外的后缀。这在使用 [BEM][] 这样的方法时特别有用，该方法使用高度结构化的类名。只要外部选择器以字母数字名称结尾（如类、ID 和元素选择器），就可以使用父选择器附加额外的文本。

[BEM]: http://getbem.com/

{% codeExample 'parent-selector-suffixes' %}
  .accordion {
    max-width: 600px;
    margin: 4rem auto;
    width: 90%;
    font-family: "Raleway", sans-serif;
    background: #f4f4f4;

    &__copy {
      display: none;
      padding: 1rem 1.5rem 2rem 1.5rem;
      color: gray;
      line-height: 1.6;
      font-size: 14px;
      font-weight: 500;

      &--open {
        display: block;
      }
    }
  }
  ===
  .accordion
    max-width: 600px
    margin: 4rem auto
    width: 90%
    font-family: "Raleway", sans-serif
    background: #f4f4f4

    &__copy
      display: none
      padding: 1rem 1.5rem 2rem 1.5rem
      color: gray
      line-height: 1.6
      font-size: 14px
      font-weight: 500

      &--open
        display: block
{% endcodeExample %}

## 在 SassScript 中

父选择器也可以在 SassScript 中使用。它是一种特殊表达式，返回当前父选择器，格式与 [选择器函数][] 所用相同：一个用逗号分隔的列表（选择器列表），其中包含空格分隔的列表（复合选择器），其中包含非引号字符串（复合选择器）。

[选择器函数]: /documentation/modules/selector#selector-values

{% codeExample 'parent-selector-sassscript' %}
  .main aside:hover,
  .sidebar p {
    parent-selector: &;
    // => ((unquote(".main") unquote("aside:hover")),
    //     (unquote(".sidebar") unquote("p")))
  }
  ===
  .main aside:hover,
  .sidebar p
    parent-selector: &
    // => ((unquote(".main") unquote("aside:hover")),
    //     (unquote(".sidebar") unquote("p")))
{% endcodeExample %}

如果 `&` 表达式在任何样式规则之外使用，它返回 `null`。由于 `null` 是 [假值][], 这意味着您可以轻松地使用它来确定一个 mixin 是否在样式规则中被调用。

[假值]: /documentation/at-rules/control/if#truthiness-and-falsiness

{% render 'code_snippets/example-if-parent-selector' %}

### 高级嵌套

您可以将 `&` 作为普通 SassScript 表达式使用，这意味着您可以将其传递给函数或包含在插值中——甚至在其他选择器中！将其与 [选择器函数][] 和 [`@at-root` 规则][] 结合使用，允许您以非常强大的方式嵌套选择器。

[选择器函数]: /documentation/modules/selector#selector-values
[`@at-root` 规则]: /documentation/at-rules/at-root

{% render 'code_snippets/example-advanced-nesting' %}

{% headsUp %}
  当 Sass 嵌套选择器时，它不知道用于生成它们的插值是什么。这意味着即使您将 `&` 用作 SassScript 表达式，它也会自动将外部选择器添加到内部选择器。这就是为什么您需要显式地使用 [`@at-root` 规则][] 告诉 Sass 不要包含外部选择器。

  [`@at-root` 规则]: /documentation/at-rules/at-root
{% endheadsUp %}