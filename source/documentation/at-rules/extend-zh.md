---
title: "@extend"
table_of_contents: true
introduction: >
  在设计页面时，经常会遇到一个类需要包含另一个类的所有样式，同时也有自己特定样式的场景。例如，[BEM 方法论](http://getbem.com/naming/)鼓励使用修饰符类，这些类与块或元素类位于同一元素上。但这会导致HTML变得杂乱，容易因为忘记同时包含两个类而出错，并且可能会将非语义化的样式问题引入到标记中。
---

<!-- TODO(jina): 我认为这些代码块应该并排显示 -->
```html
<div class="error error--serious">
  哦不！您被黑客攻击了！
</div>
```

```css
.error {
  border: 1px #f00;
  background-color: #fdd;
}

.error--serious {
  border-width: 3px;
}
```

Sass的 `@extend` 规则解决了这个问题。它写成 `@extend <selector>`，并告诉Sass一个选择器应该继承另一个选择器的样式。

{% codeExample 'extend' %}
  .error {
    border: 1px #f00;
    background-color: #fdd;

    &--serious {
      @extend .error;
      border-width: 3px;
    }
  }
  ===
  .error
    border: 1px #f00
    background-color: #fdd

    &--serious
      @extend .error
      border-width: 3px
{% endcodeExample %}

当一个类继承另一个类时，Sass会将所有匹配继承者的元素样式化，就像它们也匹配被继承的类一样。当一个类选择器继承另一个时，它的工作方式就像你将被继承的类添加到了HTML中所有已经具有继承类的元素上。你只需要写 `class="error--serious"`，Sass会确保它也被样式化为具有 `class="error"`。

当然，选择器不仅在样式规则中单独使用。Sass知道要在选择器使用的所有地方进行扩展。这确保了你的元素样式化得就像它们匹配了被继承的选择器一样。

{% codeExample 'extended-selector' %}
  .error:hover {
    background-color: #fee;
  }

  .error--serious {
    @extend .error;
    border-width: 3px;
  }
  ===
  .error:hover
    background-color: #fee


  .error--serious
    @extend .error
    border-width: 3px
{% endcodeExample %}

{% headsUp %}
  扩展在你的样式表的其余部分编译之后解析。特别是，它发生在[parent selectors][]解析之后。这意味着如果你 `@extend .error`，它不会影响 `.error { &__icon { ... } }` 中的内部选择器。这也意味着[SassScript中的父选择器][]看不到扩展的结果。

  [parent selectors]: /documentation/style-rules/parent-selector
  [SassScript中的父选择器]: /documentation/style-rules/parent-selector#in-sassscript
{% endheadsUp %}

## 工作原理

与[mixins][]不同，后者将样式复制到当前样式规则中，`@extend` 更新包含被继承选择器的样式规则，使其也包含继承选择器。在扩展选择器时，Sass进行*智能统一*：

[mixins]: /documentation/at-rules/mixin

* 它从不生成像 `#main#footer` 这样的选择器，这些选择器不可能匹配任何元素。

* 它确保复杂选择器交错，以便无论HTML元素如何嵌套都能正常工作。

* 它尽可能地去除冗余选择器，同时确保特异性不低于继承者。

* 它知道一个选择器是否匹配另一个选择器的所有内容，并可以将它们组合在一起。

* 它智能地处理[组合器][]、[通用选择器][]和[包含选择器的伪类][]。

[组合器]: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors#Combinators
[包含选择器的伪类]: https://developer.mozilla.org/en-US/docs/Web/CSS/:not
[通用选择器]: https://developer.mozilla.org/en-US/docs/Web/CSS/Universal_selectors

{% codeExample 'how-it-works' %}
  .content nav.sidebar {
    @extend .info;
  }

  // 这不会被扩展，因为 `p` 与 `nav` 不兼容。
  p.info {
    background-color: #dee9fc;
  }

  // 无法知道 `<div class="guide">` 是否在 `<div class="content">` 内部或外部，因此Sass生成两者以确保安全。
  .guide .info {
    border: 1px solid rgba(#000, 0.8);
    border-radius: 2px;
  }

  // Sass知道匹配 "main.content" 的每个元素也匹配 ".content"，并避免生成不必要的交错选择器。
  main.content .info {
    font-size: 0.8em;
  }
  ===
  .content nav.sidebar
    @extend .info


  // 这不会被扩展，因为 `p` 与 `nav` 不兼容。
  p.info
    background-color: #dee9fc


  // 无法知道 `<div class="guide">` 是否在 `<div class="content">` 内部或外部，因此Sass生成两者以确保安全。
  .guide .info
    border: 1px solid rgba(#000, 0.8)
    border-radius: 2px


  // Sass知道匹配 "main.content" 的每个元素也匹配 ".content"，并避免生成不必要的交错选择器。
  main.content .info
    font-size: 0.8em
{% endcodeExample %}

{% funFact %}
  你可以直接使用[选择器函数][]访问Sass的智能统一！[`selector.unify()` 函数][]返回匹配两个选择器交集的选择器，而[`selector.extend()` 函数][]的工作方式与 `@extend` 类似，但针对单个选择器。

  [选择器函数]: /documentation/modules/selector
  [`selector.unify()` 函数]: /documentation/modules/selector#unify
  [`selector.extend()` 函数]: /documentation/modules/selector#extend
{% endfunFact %}

{% headsUp %}
  因为 `@extend` 更新包含被继承选择器的样式规则，它们的样式在[cascade][]中的优先级基于被继承选择器的样式规则出现的位置，*而不是* `@extend` 出现的位置。这可能会让人困惑，但只要记住：这与你将被继承的类添加到HTML中的优先级相同！

  [cascade]: https://developer.mozilla.org/en-US/docs/Web/CSS/Cascade
{% endheadsUp %}

## 占位符选择器

有时你想要编写一个*仅*用于被继承的选择器规则。在这种情况下，你可以使用[占位符选择器][]，这些选择器看起来像以 `%` 开头而不是 `.` 的类选择器。任何包含占位符的选择器都不会包含在CSS输出中，但扩展它们的选择器会。

[占位符选择器]: /documentation/style-rules/placeholder-selectors

{% render 'code_snippets/example-placeholder' %}

### 私有占位符

像[模块成员][]一样，可以通过在名称前加上 `-` 或 `_` 来标记占位符选择器为私有。私有占位符选择器只能在其定义的样式表中被扩展。对于其他样式表，它看起来就像该选择器不存在一样。

[模块成员]: /documentation/at-rules/use#private-members

## 扩展范围

当一个样式表扩展一个选择器时，该扩展仅影响*上游*模块中编写的样式规则——即该样式表使用 [`@use` 规则][] 或 [`@forward` 规则][] 加载的模块，由*这些*模块加载的模块，等等。这有助于使你的 `@extend` 规则更具可预测性，确保它们仅影响你在编写时意识到的样式。

[`@use` 规则]: /documentation/at-rules/use
[`@forward` 规则]: /documentation/at-rules/forward

{% headsUp %}
  如果你使用的是 [`@import` 规则][]，扩展将没有任何作用域。它们不仅会影响你导入的每个样式表，还会影响导入你的样式表的每个样式表，这些样式表导入的其他所有内容，等等。没有 `@use`，扩展是*全局*的。

  [`@import` 规则]: /documentation/at-rules/import
{% endheadsUp %}

## 强制和可选扩展

通常，如果 `@extend` 没有匹配样式表中的任何选择器，Sass 会生成一个错误。这有助于防止拼写错误或在重命名选择器时未重命名继承自它的选择器。要求被继承选择器存在的扩展是*强制*的。

然而，这并不总是你想要的。如果你想让 `@extend` 在被继承选择器不存在时不执行任何操作，只需在末尾添加 `!optional`。

## 扩展还是混合？

扩展和[mixins][]都是在Sass中封装和重用样式的两种方式，这自然引发了何时使用哪种方式的问题。当需要使用[参数][]配置样式时，混合显然是必要的，但如果它们只是一些样式块呢？

[mixins]: /documentation/at-rules/mixin
[参数]: /documentation/at-rules/mixin/#arguments

一般来说，当表达语义类（或其他语义选择器）之间的关系时，扩展是最好的选择。因为具有 `.error--serious` 类的元素*是一个*错误，所以让它继承 `.error` 是有意义的。但对于非语义化的样式集合，编写混合可以避免级联问题，并使将来更容易配置。

{% funFact %}
  大多数Web服务器使用一种非常擅长处理重复的相同文本块的算法来压缩它们提供的CSS。这意味着，尽管混合可能会生成比扩展更多的CSS，但它们可能不会显著增加用户需要下载的量。因此，选择对你用例最有意义的特性，而不是生成最少CSS的特性！

  [gzip]: https://en.wikipedia.org/wiki/Gzip
{% endfunFact %}

## 限制

### 不允许的选择器

{% compatibility 'dart: true', 'libsass: false', 'ruby: false', 'feature: "No Compound Extensions"' %}
  LibSass 和 Ruby Sass 目前允许扩展复合选择器，如 `.message.info`。然而，这种行为不符合 `@extend` 的定义：它只对匹配 `.message.info` 的元素进行样式化，即同时匹配 `.message` 和 `.info` 的元素，而不是匹配 `.message` 或 `.info` 的元素。

  为了保持 `@extend` 的定义简单明了，并保持实现的简洁高效，该行为已被弃用，并将在未来版本中移除。

  有关更多详细信息，请参阅[重大更改页面][]。

  [重大更改页面]: /documentation/breaking-changes/extend-compound
{% endcompatibility %}

只有*简单选择器*——单独的选择器，如 `.info` 或 `a`——可以被扩展。如果可以扩展 `.message.info`，根据 `@extend` 的定义，匹配扩展者的选择器将被样式化为匹配 `.message.info`。这与同时匹配 `.message` 和 `.info` 是相同的，因此编写 `@extend .message, .info` 而不是 `@extend .message.info` 没有任何好处。

类似地，如果可以扩展 `.main .info`，它几乎与单独扩展 `.info` 一样。这些细微的差别并不值得看起来像是在做一些实质性不同的事情，所以这也被禁止。

{% codeExample 'disallowed-selectors', false %}
  .alert {
    @extend .message.info;
    //      ^^^^^^^^^^^^^
    // 错误：写成 @extend .message, .info。

    @extend .main .info;
    //      ^^^^^^^^^^^
    // 错误：写成 @extend .info。
  }
  ===
  .alert
    @extend .message.info
    //      ^^^^^^^^^^^^^
    // 错误：写成 @extend .message, .info。

    @extend .main .info
    //      ^^^^^^^^^^^
    // 错误：写成 @extend .info。
{% endcodeExample %}

### HTML 试探

当 `@extend` [交错复杂选择器][]时，它不会生成所有可能的祖先选择器组合。它可以生成的许多选择器实际上不太可能匹配真实的HTML，生成所有这些选择器会使样式表变得过大而几乎没有实际价值。相反，它使用[试探法][]：它假设每个选择器的祖先将是独立的，不会与其他选择器的祖先交错。

[交错复杂选择器]: #how-it-works
[试探法]: https://en.wikipedia.org/wiki/Heuristic

{% codeExample 'html-heuristics' %}
  header .warning li {
    font-weight: bold;
  }

  aside .notice dd {
    // Sass 不会生成 CSS 来匹配
    //
    // <header>
    //   <aside>
    //     <div class="warning">
    //       <div class="notice">
    //         <dd>...</dd>
    //       </div>
    //     </div>
    //   </aside>
    // </header>
    //
    // 中的 <dd>，
    // 因为匹配所有这些元素需要我们生成九个新的选择器，而不是仅仅两个。
    @extend li;
  }
  ===
  header .warning li
    font-weight: bold


  aside .notice dd
    // Sass 不会生成 CSS 来匹配
    //
    // <header>
    //   <aside>
    //     <div class="warning">
    //       <div class="notice">
    //         <dd>...</dd>
    //       </div>
    //     </div>
    //   </aside>
    // </header>
    //
    // 中的 <dd>，
    // 因为匹配所有这些元素需要我们生成九个新的选择器，而不是仅仅两个。
    @extend li
{% endcodeExample %}

### 在 `@media` 中使用扩展

虽然 `@extend` 允许在 [`@media` 和其他 CSS 规则][] 内使用，但它不允许扩展出现在其规则之外的选择器。这是因为扩展选择器仅在给定的媒体上下文中应用，并且没有方法可以在不复制整个样式规则的情况下确保该限制在生成的选择器中得到保留。

[`@media` 和其他 CSS 规则]: /documentation/at-rules/css

{% codeExample 'extend-media', false %}
  @media screen and (max-width: 600px) {
    .error--serious {
      @extend .error;
      //      ^^^^^^
      // 错误：".error" 在 @media 中被扩展，但在其外部使用。
    }
  }

  .error {
    border: 1px #f00;
    background-color: #fdd;
  }
  ===
  @media screen and (max-width: 600px)
    .error--serious
      @extend .error
      //      ^^^^^^
      // 错误：".error" 在 @media 中被扩展，但在其外部使用。



  .error
    border: 1px #f00
    background-color: #fdd
{% endcodeExample %}