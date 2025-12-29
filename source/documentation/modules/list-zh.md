---
title: sass:list
---

{% render 'doc_snippets/built-in-module-status' %}

{% funFact %}
  在 Sass 中，每个 [map][] 都被视为一个列表，其中每个键/值对都是一个包含两个元素的列表。例如，`(1: 2, 3: 4)` 被视为 `(1 2, 3 4)`。因此，所有这些函数同样适用于映射！

  [map]: /documentation/values/maps

  单个值也被视为列表。所有这些函数将 `1px` 视为包含值 `1px` 的列表。
{% endfunFact %}

{% function 'list.append($list, $val, $separator: auto)', 'append($list, $val, $separator: auto)',  'returns:list' %}
  返回一个 `$list` 的副本，并在末尾添加 `$val`。

  如果 `$separator` 是 `comma`，`space` 或 `slash`，则返回的列表分别是逗号分隔、空格分隔或斜杠分隔。如果它是 `auto`（默认值），则返回的列表将使用与 `$list` 相同的分隔符（如果 `$list` 没有分隔符，则使用空格）。其他值不允许。

  [separator]: /documentation/values/lists

  请注意，与 [`list.join()`](#join) 不同，如果 `$val` 是一个列表，则它会被嵌套在返回的列表中，而不是将其所有元素添加到返回的列表中。

  {% codeExample 'list-append' %}
    @use 'sass:list';

    @debug list.append(10px 20px, 30px); // 10px 20px 30px
    @debug list.append((blue, red), green); // blue, red, green
    @debug list.append(10px 20px, 30px 40px); // 10px 20px (30px 40px)
    @debug list.append(10px, 20px, $separator: comma); // 10px, 20px
    @debug list.append((blue, red), green, $separator: space); // blue red green
    ===
    @use 'sass:list'

    @debug list.append(10px 20px, 30px)  // 10px 20px 30px
    @debug list.append((blue, red), green)  // blue, red, green
    @debug list.append(10px 20px, 30px 40px)  // 10px 20px (30px 40px)
    @debug list.append(10px, 20px, $separator: comma)  // 10px, 20px
    @debug list.append((blue, red), green, $separator: space)  // blue red green
  {% endcodeExample %}
{% endfunction %}

{% function 'list.index($list, $value)', 'index($list, $value)', 'returns:number | null' %}
  返回 `$value` 在 `$list` 中的 [索引][]。

  [index]: /documentation/values/lists#indexes

  如果 `$value` 不在 `$list` 中，则返回 [`null`][]。如果 `$value` 在 `$list` 中出现多次，则返回其第一次出现的索引。

  [`null`]: /documentation/values/null

  {% render 'code_snippets/example-list-index' %}
{% endfunction %}

{% function 'list.is-bracketed($list)', 'is-bracketed($list)', 'returns:boolean' %}
  返回 `$list` 是否具有方括号。

  {% codeExample 'list-is-bracketed' %}
    @use 'sass:list';

    @debug list.is-bracketed(1px 2px 3px); // false
    @debug list.is-bracketed([1px, 2px, 3px]); // true
    ===
    @use 'sass:list'

    @debug list.is-bracketed(1px 2px 3px)  // false
    @debug list.is-bracketed([1px, 2px, 3px])  // true
  {% endcodeExample %}
{% endfunction %}

{% function 'list.join($list1, $list2, $separator: auto, $bracketed: auto)', 'join($list1, $list2, $separator: auto, $bracketed: auto)', 'returns:list' %}
  返回一个新列表，包含 `$list1` 的元素后跟 `$list2` 的元素。

  {% headsUp %}
    由于单个值被视为单元素列表，因此可以使用 `list.join()` 将值添加到列表末尾。但是，*不建议这样做*，因为如果该值是列表，则会进行连接，这可能不是您期望的行为。

    使用 [`list.append()`](#append) 而不是将单个值添加到列表中。仅在将两个列表合并为一个时才使用 `list.join()`。
  {% endheadsUp %}

  如果 `$separator` 是 `comma`，`space` 或 `slash`，则返回的列表分别是逗号分隔、空格分隔或斜杠分隔。如果它是 `auto`（默认值），则返回的列表将使用与 `$list1` 相同的分隔符（如果它有分隔符），否则使用 `$list2` 的分隔符（如果它有分隔符），否则使用空格。其他值不允许。

  如果 `$bracketed` 是 `auto`（默认值），则返回的列表将与 `$list1` 一样具有方括号。否则，如果 `$bracketed` 为 [真值]，则返回的列表具有方括号；如果 `$bracketed` 为假值，则没有方括号。

  [真值]: /documentation/values/booleans#truthiness-and-falsiness

  {% codeExample 'list-join' %}
    @use 'sass:list';

    @debug list.join(10px 20px, 30px 40px); // 10px 20px 30px 40px
    @debug list.join((blue, red), (#abc, #def)); // blue, red, #abc, #def
    @debug list.join(10px, 20px); // 10px 20px
    @debug list.join(10px, 20px, $separator: comma); // 10px, 20px
    @debug list.join((blue, red), (#abc, #def), $separator: space); // blue red #abc #def
    @debug list.join([10px], 20px); // [10px 20px]
    @debug list.join(10px, 20px, $bracketed: true); // [10px 20px]
    ===
    @use 'sass:list'

    @debug list.join(10px 20px, 30px 40px)  // 10px 20px 30px 40px
    @debug list.join((blue, red), (#abc, #def))  // blue, red, #abc, #def
    @debug list.join(10px, 20px)  // 10px 20px
    @debug list.join(10px, 20px, comma)  // 10px, 20px
    @debug list.join((blue, red), (#abc, #def), space)  // blue red #abc #def
    @debug list.join([10px], 20px)  // [10px 20px]
    @debug list.join(10px, 20px, $bracketed: true)  // [10px 20px]
  {% endcodeExample %}
{% endfunction %}

{% function 'list.length($list)', 'length($list)', 'returns:number' %}
  返回 `$list` 的长度。

  这也可以返回映射中的键值对数量。

  {% codeExample 'list-length' %}
    @use 'sass:list';

    @debug list.length(10px); // 1
    @debug list.length(10px 20px 30px); // 3
    @debug list.length((width: 10px, height: 20px)); // 2
    ===
    @use 'sass:list'

    @debug list.length(10px)  // 1
    @debug list.length(10px 20px 30px)  // 3
    @debug list.length((width: 10px, height: 20px))  // 2
  {% endcodeExample %}
{% endfunction %}

{% function 'list.separator($list)', 'list-separator($list)', 'returns:unquoted string' %}
  返回 `$list` 使用的分隔符的名称，可以是 `space`，`comma` 或 `slash`。

  如果 `$list` 没有分隔符，则返回 `space`。

  {% codeExample 'list-separator' %}
    @use 'sass:list';

    @debug list.separator(1px 2px 3px); // space
    @debug list.separator((1px, 2px, 3px)); // comma
    @debug list.separator('Helvetica'); // space
    @debug list.separator(()); // space
    ===
    @use 'sass:list'

    @debug list.separator(1px 2px 3px)  // space
    @debug list.separator((1px, 2px, 3px))  // comma
    @debug list.separator('Helvetica')  // space
    @debug list.separator(())  // space
  {% endcodeExample %}
{% endfunction %}

{% function 'list.nth($list, $n)', 'nth($list, $n)' %}
  返回 `$list` 中 [索引][] `$n` 处的元素。

  [index]: /documentation/values/lists#indexes

  如果 `$n` 为负数，则从 `$list` 末尾开始计数。如果索引 `$n` 处没有元素，则抛出错误。

  {% render 'code_snippets/example-list-nth' %}
{% endfunction %}

{% function 'list.set-nth($list, $n, $value)', 'set-nth($list, $n, $value)', 'returns:list' %}
  返回一个 `$list` 的副本，并将 [索引][] `$n` 处的元素替换为 `$value`。

  [index]: /documentation/values/lists#indexes

  如果 `$n` 为负数，则从 `$list` 末尾开始计数。如果索引 `$n` 处没有现有元素，则抛出错误。

  {% codeExample 'list-set-nth' %}
    @use 'sass:list';

    @debug list.set-nth(10px 20px 30px, 1, 2em); // 2em 20px 30px
    @debug list.set-nth(10px 20px 30px, -1, 8em); // 10px, 20px, 8em
    @debug list.set-nth((Helvetica, Arial, sans-serif), 3, Roboto); // Helvetica, Arial, Roboto
    ===
    @use 'sass:list'

    @debug list.set-nth(10px 20px 30px, 1, 2em); // 2em 20px 30px
    @debug list.set-nth(10px 20px 30px, -1, 8em); // 10px, 20px, 8em
    @debug list.set-nth((Helvetica, Arial, sans-serif), 3, Roboto); // Helvetica, Arial, Roboto
  {% endcodeExample %}
{% endfunction %}

{% function 'list.slash($elements...)', 'returns:list' %}
  返回一个包含 `$elements` 的斜杠分隔列表。

  {% headsUp %}
    此函数是创建斜杠分隔列表的临时解决方案。最终，它们将直接用斜杠编写，例如 `1px / 2px / solid`，但目前 [斜杠用于除法]，因此在旧语法被移除之前，Sass 无法使用它们用于新语法。

    [斜杠用于除法]: /documentation/breaking-changes/slash-div
  {% endheadsUp %}

  {% codeExample 'list-slash' %}
    @use 'sass:list';

    @debug list.slash(1px, 50px, 100px); // 1px / 50px / 100px
    ===
    @use 'sass:list'

    @debug list.slash(1px, 50px, 100px)  // 1px / 50px / 100px
  {% endcodeExample %}
{% endfunction %}

{% function 'list.zip($lists...)', 'zip($lists...)', 'returns:list' %}
  将 `$lists` 中的每个列表合并为一个子列表的单一列表。

  返回列表中的每个元素包含 `$lists` 中该位置的所有元素。返回的列表长度与 `$lists` 中最短列表的长度相同。

  返回的列表始终是逗号分隔的，子列表始终是空格分隔的。

  {% codeExample 'list-zip' %}
    @use 'sass:list';

    @debug list.zip(10px 50px 100px, short mid long); // 10px short, 50px mid, 100px long
    @debug list.zip(10px 50px 100px, short mid); // 10px short, 50px mid
    ===
    @use 'sass:list'

    @debug list.zip(10px 50px 100px, short mid long)  // 10px short, 50px mid, 100px long
    @debug list.zip(10px 50px 100px, short mid)  // 10px short, 50px mid
  {% endcodeExample %}
{% endfunction %}