---
title: "@debug"
introduction: >
  有时在开发样式表时查看[变量](/documentation/variables)或[表达式](/documentation/syntax/structure#expressions)的值会很有用。`@debug`规则就是为了这个目的：它写成`@debug <expression>`，并打印该表达式的值，同时显示文件名和行号。
---

{% codeExample 'debug', false %}
  @mixin inset-divider-offset($offset, $padding) {
    $divider-offset: (2 * $padding) + $offset;
    @debug "divider offset: #{$divider-offset}";

    margin-left: $divider-offset;
    width: calc(100% - #{$divider-offset});
  }
  ===
  @mixin inset-divider-offset($offset, $padding)
    $divider-offset: (2 * $padding) + $offset
    @debug "divider offset: #{$divider-offset}"

    margin-left: $divider-offset
    width: calc(100% - #{$divider-offset})
{% endcodeExample %}

调试消息的确切格式因实现而异。这是在Dart Sass中的样子：

```
test.scss:3 Debug: divider offset: 132px
```

{% funFact %}
  你可以向`@debug`传递任何值，而不仅仅是字符串！它打印该值的表示形式，与[`meta.inspect()` function][]相同。

  [`meta.inspect()` function]: /documentation/modules/meta#inspect
{% endfunFact %}