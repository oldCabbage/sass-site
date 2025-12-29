---
title: 映射
table_of_contents: true
---

映射在 Sass 中用于存储键值对，并通过键轻松查找对应的值。它们的写法是 `(<表达式>: <表达式>, <表达式>: <表达式>)`。`:` 之前的 [表达式](/documentation/syntax/structure#expressions) 是键，`:` 之后的表达式是与该键关联的值。键必须是唯一的，但值可以重复。与 [列表](/documentation/values/lists) 不同，映射 *必须* 用括号括起来。没有键值对的映射写法是 `()`。

{% funFact %}
  仔细的读者可能会注意到，空映射 `()` 的写法与空列表相同。这是因为它既是映射又是列表。事实上，*所有* 映射都被视为列表！每个映射都被视为一个列表，其中包含每个键值对的两个元素列表。例如，`(1: 2, 3: 4)` 被视为 `(1 2, 3 4)`。
{% endfunFact %}

映射允许任何 Sass 值作为键。[`==` 操作符][] 用于确定两个键是否相同。

[`==` 操作符]: /documentation/operators/equality

{% headsUp %}
  大多数情况下，使用 [带引号的字符串][] 而不是 [不带引号的字符串][] 作为映射键是个好主意。这是因为某些值，如颜色名称，可能 *看起来* 像不带引号的字符串，但实际上可能是其他类型。为了避免将来出现混淆问题，最好使用引号！

  [带引号的字符串]: /documentation/values/strings#quoted
  [不带引号的字符串]: /documentation/values/strings#unquoted
{% endheadsUp %}

## 使用映射

由于映射不是有效的 CSS 值，它们本身并没有太多作用。这就是为什么 Sass 提供了许多 [函数][] 来创建映射并访问它们包含的值。

[函数]: /documentation/modules/map

### 查找值

映射的主要功能是将键与值关联起来，因此自然有一种方法可以获取与某个键关联的值：[`map.get($map, $key)` 函数][]！这个函数返回映射中与给定键关联的值。如果映射中不包含该键，则返回 [`null`][]。

[`map.get($map, $key)` 函数]: /documentation/modules/map#get
[`null`]: /documentation/values/null

{% render 'code_snippets/example-map-get' %}

### 遍历每个键值对

这实际上并没有使用函数，但仍然是使用映射的最常见方式之一。[`@each` 规则][] 会为映射中的每个键值对评估一段样式。键和值被赋给变量，以便在块中轻松访问。

[`@each` 规则]: /documentation/at-rules/control/each

{% render 'code_snippets/example-each-map' %}

### 添加到映射

向映射中添加新键值对或替换现有键的值也很有用。[`map.set($map, $key, $value)` 函数][] 可以做到这一点：它返回 `$map` 的一个副本，并将 `$key` 的值设置为 `$value`。

[`map.set($map, $key, $value)` 函数]: /documentation/modules/map#set

{% codeExample 'maps', false %}
  @use "sass:map";

  $font-weights: ("regular": 400, "medium": 500, "bold": 700);

  @debug map.set($font-weights, "extra-bold", 900);
  // ("regular": 400, "medium": 500, "bold": 700, "extra-bold": 900)
  @debug map.set($font-weights, "bold", 900);
  // ("regular": 400, "medium": 500, "bold": 900)
  ===
  @use "sass:map"

  $font-weights: ("regular": 400, "medium": 500, "bold": 700)

  @debug map.set($font-weights, "extra-bold": 900)
  // ("regular": 400, "medium": 500, "bold": 700, "extra-bold": 900)
  @debug map.set($font-weights, "bold", 900)
  // ("regular": 400, "medium": 500, "bold": 900)
{% endcodeExample %}

除了逐个设置值，还可以使用 [`map.merge($map1, $map2)`][] 合并两个现有映射。

[`map.merge($map1, $map2)`]: /documentation/modules/map#merge

{% codeExample 'map-merge', false %}
  @use "sass:map";

  $light-weights: ("lightest": 100, "light": 300);
  $heavy-weights: ("medium": 500, "bold": 700);

  @debug map.merge($light-weights, $heavy-weights);
  // ("lightest": 100, "light": 300, "medium": 500, "bold": 700)
  ===
  @use "sass:map"

  $light-weights: ("lightest": 100, "light": 300)
  $heavy-weights: ("medium": 500, "bold": 700)

  @debug map.merge($light-weights, $heavy-weights)
  // ("lightest": 100, "light": 300, "medium": 500, "bold": 700)
{% endcodeExample %}

如果两个映射具有相同的键，返回的映射将使用第二个映射的值。

{% codeExample 'map-same-keys', false %}
  @use "sass:map";

  $weights: ("light": 300, "medium": 500);

  @debug map.merge($weights, ("medium": 700));
  // ("light": 300, "medium": 700)
  ===
  @use "sass:map";

  $weights: ("light": 300, "medium": 500)

  @debug map.merge($weights, ("medium": 700))
  // ("light": 300, "medium": 700)
{% endcodeExample %}

请注意，由于 Sass 映射是 [不可变的][], `map.set()` 和 `map.merge()` 不会修改原始映射。

[不可变的]: #immutability

## 不可变性

Sass 中的映射是 *不可变的*，这意味着映射值的内容永远不会改变。Sass 的映射函数都会返回新的映射，而不是修改原始映射。不可变性有助于避免在样式表的不同部分共享相同映射时可能出现的许多难以察觉的错误。

不过，您仍然可以通过将新的映射赋值给相同的变量来随时间更新状态。这通常在函数和混合宏中用于在映射中跟踪配置。

{% codeExample 'immutability', false %}
  @use "sass:map";

  $prefixes-by-browser: ("firefox": moz, "safari": webkit, "ie": ms);

  @mixin add-browser-prefix($browser, $prefix) {
    $prefixes-by-browser: map.merge($prefixes-by-browser, ($browser: $prefix)) !global;
  }

  @include add-browser-prefix("opera", o);
  @debug $prefixes-by-browser;
  // ("firefox": moz, "safari": webkit, "ie": ms, "opera": o)
  ===
  @use "sass:map"

  $prefixes-by-browser: ("firefox": moz, "safari": webkit, "ie": ms)

  @mixin add-browser-prefix($browser, $prefix)
    $prefixes-by-browser: map.merge($prefixes-by-browser, ($browser: $prefix)) !global


  @include add-browser-prefix("opera", o)
  @debug $prefixes-by-browser
  // ("firefox": moz, "safari": webkit, "ie": ms, "opera": o)
{% endcodeExample %}