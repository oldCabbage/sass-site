---
title: sass:selector
---

{% render 'doc_snippets/built-in-module-status' %}

## 选择器值

此模块中的函数用于检查和操作选择器。每当它们返回一个选择器时，它总是以逗号分隔的 [列表][]（选择器列表）形式返回，该列表包含以空格分隔的列表（复合选择器），这些列表包含 [非引号字符串][]（基本选择器）。例如，选择器 `.main aside:hover, .sidebar p` 将会被返回为：

[list]: /documentation/values/lists
[unquoted strings]: /documentation/values/strings#unquoted

```scss
@debug ((unquote(".main") unquote("aside:hover")),
        (unquote(".sidebar") unquote("p")));
// .main aside:hover, .sidebar p
```

传递给这些函数的选择器参数可以是相同的格式，但也可以只是普通的字符串（引号或非引号），或者它们的组合。例如，`".main aside:hover, .sidebar p"` 是一个有效的选择器参数。

{% function 'selector.is-superselector($super, $sub)', 'is-superselector($super, $sub)', 'returns:boolean' %}
  返回选择器 `$super` 是否匹配选择器 `$sub` 匹配的所有元素。

  即使 `$super` 匹配的元素 *更多* 于 `$sub`，也会返回 true。

  `$super` 和 `$sub` 选择器可以包含 [占位符选择器][]，但不能包含 [父选择器][]。

  [placeholder selectors]: /documentation/style-rules/placeholder-selectors
  [parent selectors]: /documentation/style-rules/parent-selector

  {% codeExample 'is-superselector' %}
    @use "sass:selector";

    @debug selector.is-superselector("a", "a.disabled"); // true
    @debug selector.is-superselector("a.disabled", "a"); // false
    @debug selector.is-superselector("a", "sidebar a"); // true
    @debug selector.is-superselector("sidebar a", "a"); // false
    @debug selector.is-superselector("a", "a"); // true
    ===
    @use "sass:selector"

    @debug selector.is-superselector("a", "a.disabled")  // true
    @debug selector.is-superselector("a.disabled", "a")  // false
    @debug selector.is-superselector("a", "sidebar a")  // true
    @debug selector.is-superselector("sidebar a", "a")  // false
    @debug selector.is-superselector("a", "a")  // true
  {% endcodeExample %}
{% endfunction %}

{% function 'selector.append($selectors...)', 'selector-append($selectors...)', 'returns:selector' %}
  将 `$selectors` 组合在一起，不使用 [后代组合器][]—也就是说，它们之间没有空格。

  [descendant combinators]: https://developer.mozilla.org/en-US/docs/Web/CSS/Descendant_selectors

  如果 `$selectors` 中的任何选择器是选择器列表，则每个复合选择器会分别组合。

  `$selectors` 可以包含 [占位符选择器][]，但不能包含 [父选择器][]。

  [placeholder selectors]: /documentation/style-rules/placeholder-selectors
  [parent selectors]: /documentation/style-rules/parent-selector

  参见 [`selector.nest()`](#nest)。

  {% codeExample 'append' %}
    @use "sass:selector";

    @debug selector.append("a", ".disabled"); // a.disabled
    @debug selector.append(".accordion", "__copy"); // .accordion__copy
    @debug selector.append(".accordion", "__copy, __image");
    // .accordion__copy, .accordion__image
    ===
    @use "sass:selector"

    @debug selector.append("a", ".disabled")  // a.disabled
    @debug selector.append(".accordion", "__copy")  // .accordion__copy
    @debug selector.append(".accordion", "__copy, __image")
    // .accordion__copy, .accordion__image
  {% endcodeExample %}
{% endfunction %}

{% function 'selector.extend($selector, $extendee, $extender)', 'selector-extend($selector, $extendee, $extender)', 'returns:selector' %}
  通过 [`@extend` 规则][] 扩展 `$selector`。

  [`@extend` rule]: /documentation/at-rules/extend

  返回一个 `$selector` 的副本，并应用以下 `@extend` 规则：

  ```scss
  #{$extender} {
    @extend #{$extendee};
  }
  ```

  换句话说，将 `$selector` 中的所有 `$extendee` 实例替换为 `$extendee, $extender`。如果 `$selector` 不包含 `$extendee`，则原样返回。

  `$selector`、`$extendee` 和 `$extender` 选择器可以包含 [占位符选择器][]，但不能包含 [父选择器][]。

  [placeholder selectors]: /documentation/style-rules/placeholder-selectors
  [parent selectors]: /documentation/style-rules/parent-selector

  参见 [`selector.replace()`](#replace)。

  {% codeExample 'extend' %}
    @use "sass:selector";

    @debug selector.extend("a.disabled", "a", ".link"); // a.disabled, .link.disabled
    @debug selector.extend("a.disabled", "h1", "h2"); // a.disabled
    @debug selector.extend(".guide .info", ".info", ".content nav.sidebar");
    // .guide .info, .guide .content nav.sidebar, .content .guide nav.sidebar
    ===
    @use "sass:selector"

    @debug selector.extend("a.disabled", "a", ".link")  // a.disabled, .link.disabled
    @debug selector.extend("a.disabled", "h1", "h2")  // a.disabled
    @debug selector.extend(".guide .info", ".info", ".content nav.sidebar")
    // .guide .info, .guide .content nav.sidebar, .content .guide nav.sidebar
  {% endcodeExample %}
{% endfunction %}

{% function 'selector.nest($selectors...)', 'selector-nest($selectors...)', 'returns:selector' %}
  将 `$selectors` 组合在一起，就像它们在样式表中嵌套一样。

  `$selectors` 可以包含 [占位符选择器][]。与其他选择器函数不同，除了第一个之外的所有选择器都可以包含 [父选择器][]。

  [placeholder selectors]: /documentation/style-rules/placeholder-selectors
  [parent selectors]: /documentation/style-rules/parent-selector

  参见 [`selector.append()`](#append)。

  {% codeExample 'nest' %}
    @use "sass:selector";

    @debug selector.nest("ul", "li"); // ul li
    @debug selector.nest(".alert, .warning", "p"); // .alert p, .warning p
    @debug selector.nest(".alert", "&:hover"); // .alert:hover
    @debug selector.nest(".accordion", "&__copy"); // .accordion__copy
    ===
    @use "sass:selector"

    @debug selector.nest("ul", "li")  // ul li
    @debug selector.nest(".alert, .warning", "p")  // .alert p, .warning p
    @debug selector.nest(".alert", "&:hover")  // .alert:hover
    @debug selector.nest(".accordion", "&__copy")  // .accordion__copy
  {% endcodeExample %}
{% endfunction %}

{% function 'selector.parse($selector)', 'selector-parse($selector)', 'returns:selector' %}
  返回 `$selector` 的 [选择器值](#selector-values) 格式。

  {% codeExample 'parse' %}
    @use "sass:selector";

    @debug selector.parse(".main aside:hover, .sidebar p");
    // ((unquote(".main") unquote("aside:hover")),
    //  (unquote(".sidebar") unquote("p")))
    ===
    @use "sass:selector"

    @debug selector.parse(".main aside:hover, .sidebar p")
    // ((unquote(".main") unquote("aside:hover")),
    //  (unquote(".sidebar") unquote("p")))
  {% endcodeExample %}
{% endfunction %}

{% function 'selector.replace($selector, $original, $replacement)', 'selector-replace($selector, $original, $replacement)', 'returns:selector' %}
  返回 `$selector` 的副本，其中所有 `$original` 实例都被 `$replacement` 替换。

  这使用了 [`@extend` 规则][] 的 [智能统一][] 来确保 `$replacement` 能够无缝地集成到 `$selector` 中。如果 `$selector` 不包含 `$original`，则原样返回。

  [`@extend` rule]: /documentation/at-rules/extend
  [intelligent unification]: /documentation/at-rules/extend#how-it-works

  `$selector`、`$original` 和 `$replacement` 选择器可以包含 [占位符选择器][]，但不能包含 [父选择器][]。

  [placeholder selectors]: /documentation/style-rules/placeholder-selectors
  [parent selectors]: /documentation/style-rules/parent-selector

  参见 [`selector.extend()`](#extend)。

  {% codeExample 'replace' %}
    @use "sass:selector";

    @debug selector.replace("a.disabled", "a", ".link"); // .link.disabled
    @debug selector.replace("a.disabled", "h1", "h2"); // a.disabled
    @debug selector.replace(".guide .info", ".info", ".content nav.sidebar");
    // .guide .content nav.sidebar, .content .guide nav.sidebar
    ===
    @use "sass:selector"

    @debug selector.replace("a.disabled", "a", ".link")  // .link.disabled
    @debug selector.replace("a.disabled", "h1", "h2")  // a.disabled
    @debug selector.replace(".guide .info", ".info", ".content nav.sidebar")
    // .guide .content nav.sidebar, .content .guide nav.sidebar
  {% endcodeExample %}
{% endfunction %}

{% function 'selector.unify($selector1, $selector2)', 'selector-unify($selector1, $selector2)', 'returns:selector | null' %}
  返回一个选择器，该选择器仅匹配同时被 `$selector1` 和 `$selector2` 匹配的元素。

  如果 `$selector1` 和 `$selector2` 不匹配任何相同的元素，或者没有选择器可以表达它们的交集，则返回 `null`。

  与 [`@extend` 规则][] 生成的选择器类似，返回的选择器不一定匹配由 `$selector1` 和 `$selector2` 都匹配的所有元素，特别是当它们都是复杂选择器时。

  [`@extend` rule]: /documentation/at-rules/extend#html-heuristics

  {% codeExample 'unify' %}
    @use "sass:selector";

    @debug selector.unify("a", ".disabled"); // a.disabled
    @debug selector.unify("a.disabled", "a.outgoing"); // a.disabled.outgoing
    @debug selector.unify("a", "h1"); // null
    @debug selector.unify(".warning a", "main a"); // .warning main a, main .warning a
    ===
    @use "sass:selector"

    @debug selector.unify("a", ".disabled")  // a.disabled
    @debug selector.unify("a.disabled", "a.outgoing")  // a.disabled.outgoing
    @debug selector.unify("a", "h1")  // null
    @debug selector.unify(".warning a", "main a")  // .warning main a, main .warning a
  {% endcodeExample %}
{% endfunction %}

{% function 'selector.simple-selectors($selector)', 'simple-selectors($selector)', 'returns:list' %}
  返回 `$selector` 中的基本选择器列表。

  `$selector` 必须是一个包含复合选择器的单个字符串。这意味着它不能包含组合器（包括空格）或逗号。

  返回的列表是逗号分隔的，基本选择器是非引号字符串。

  {% codeExample 'simple-selectors' %}
    @use "sass:selector";

    @debug selector.simple-selectors("a.disabled"); // a, .disabled
    @debug selector.simple-selectors("main.blog:after"); // main, .blog, :after
    ===
    @use "sass:selector"

    @debug selector.simple-selectors("a.disabled")  // a, .disabled
    @debug selector.simple-selectors("main.blog:after")  // main, .blog, :after
  {% endcodeExample %}
{% endfunction %}