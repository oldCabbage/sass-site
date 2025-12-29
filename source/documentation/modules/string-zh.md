---
title: sass:string
---

{% render 'doc_snippets/built-in-module-status' %}

{% function 'string.quote($string)', 'quote($string)', 'returns:string' %}
  返回 `$string` 作为带引号的字符串。

  {% codeExample 'quote' %}
    @use "sass:string";

    @debug string.quote(Helvetica); // "Helvetica"
    @debug string.quote("Helvetica"); // "Helvetica"
    ===
    @use "sass:string"

    @debug string.quote(Helvetica)  // "Helvetica"
    @debug string.quote("Helvetica")  // "Helvetica"
  {% endcodeExample %}
{% endfunction %}

{% function 'string.index($string, $substring)', 'str-index($string, $substring)', 'returns:number' %}
  返回 `$substring` 在 `$string` 中的第一个 [索引][index]，如果 `$string` 不包含 `$substring`，则返回 `null`。

  [index]: /documentation/values/strings#string-indexes

  {% codeExample 'index' %}
    @use "sass:string";

    @debug string.index("Helvetica Neue", "Helvetica"); // 1
    @debug string.index("Helvetica Neue", "Neue"); // 11
    ===
    @use "sass:string"

    @debug string.index("Helvetica Neue", "Helvetica")  // 1
    @debug string.index("Helvetica Neue", "Neue")  // 11
  {% endcodeExample %}
{% endfunction %}

{% function 'string.insert($string, $insert, $index)', 'str-insert($string, $insert, $index)', 'returns:string' %}
  返回在 [`$index`][] 处插入 `$insert` 后的 `$string` 的副本。

  [`$index`]: /documentation/values/strings#string-indexes

  {% codeExample 'insert' %}
    @use "sass:string";

    @debug string.insert("Roboto Bold", " Mono", 7); // "Roboto Mono Bold"
    @debug string.insert("Roboto Bold", " Mono", -6); // "Roboto Mono Bold"
    ===
    @use "sass:string"

    @debug string.insert("Roboto Bold", " Mono", 7)  // "Roboto Mono Bold"
    @debug string.insert("Roboto Bold", " Mono", -6)  // "Roboto Mono Bold"
  {% endcodeExample %}

  如果 `$index` 大于 `$string` 的长度，`$insert` 将被添加到末尾。如果 `$index` 小于字符串的负长度，`$insert` 将被添加到开头。

  {% codeExample 'insert-2' %}
    @use "sass:string";

    @debug string.insert("Roboto", " Bold", 100); // "Roboto Bold"
    @debug string.insert("Bold", "Roboto ", -100); // "Roboto Bold"
    ===
    @use "sass:string"

    @debug string.insert("Roboto", " Bold", 100)  // "Roboto Bold"
    @debug string.insert("Bold", "Roboto ", -100)  // "Roboto Bold"
  {% endcodeExample %}
{% endfunction %}

{% function 'string.length($string)', 'str-length($string)', 'returns:number' %}
  返回 `$string` 中的字符数。

  {% codeExample 'length' %}
    @use "sass:string";

    @debug string.length("Helvetica Neue"); // 14
    @debug string.length(bold); // 4
    @debug string.length(""); // 0
    ===
    @use "sass:string"

    @debug string.length("Helvetica Neue")  // 14
    @debug string.length(bold)  // 4
    @debug string.length("")  // 0
  {% endcodeExample %}
{% endfunction %}

{% function 'string.slice($string, $start-at, $end-at: -1)', 'str-slice($string, $start-at, $end-at: -1)', 'returns:string' %}
  返回从 [索引][] `$start-at` 开始到索引 `$end-at` 结束（均包含）的 `$string` 的片段。

  [index]: /documentation/values/strings#string-indexes

  {% codeExample 'slice' %}
    @use "sass:string";

    @debug string.slice("Helvetica Neue", 11); // "Neue"
    @debug string.slice("Helvetica Neue", 1, 3); // "Hel"
    @debug string.slice("Helvetica Neue", 1, -6); // "Helvetica"
    ===
    @use "sass:string"

    @debug string.slice("Helvetica Neue", 11)  // "Neue"
    @debug string.slice("Helvetica Neue", 1, 3)  // "Hel"
    @debug string.slice("Helvetica Neue", 1, -6)  // "Helvetica"
  {% endcodeExample %}
{% endfunction %}

{% function 'string.split($string, $separator, $limit: null)', 'returns:list' %}
  {% compatibility 'dart: "1.57.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回 `$string` 由 `$separator` 分隔的子字符串的括号包围的逗号分隔列表。这些子字符串中不包含 `$separator`。

  如果 `$limit` 是一个大于或等于 `1` 的数字，那么最多会根据 `$limit` 个 `$separator` 进行分割（因此最多返回 `$limit + 1` 个字符串）。最后一个子字符串包含剩余的字符串，包括任何剩余的 `$separator`。

  {% codeExample 'split' %}
    @use "sass:string";

    @debug string.split("Segoe UI Emoji", " "); // ["Segoe", "UI", "Emoji"]
    @debug string.split("Segoe UI Emoji", " ", $limit: 1); // ["Segoe", "UI Emoji"]
    ===
    @use "sass:string"

    @debug string.split("Segoe UI Emoji", " ")  // ["Segoe", "UI", "Emoji"]
    @debug string.split("Segoe UI Emoji", " ", $limit: 1)  // ["Segoe", "UI Emoji"]
  {% endcodeExample %}
{% endfunction %}

{% function 'string.to-upper-case($string)', 'to-upper-case($string)', 'returns:string' %}
  返回将 `$string` 中的 [ASCII][] 字母转换为大写的副本。

  [ASCII]: https://en.wikipedia.org/wiki/ASCII

  {% codeExample 'to-upper-case' %}
    @use "sass:string";

    @debug string.to-upper-case("Bold"); // "BOLD"
    @debug string.to-upper-case(sans-serif); // SANS-SERIF
    ===
    @use "sass:string"

    @debug string.to-upper-case("Bold")  // "BOLD"
    @debug string.to-upper-case(sans-serif)  // SANS-SERIF
  {% endcodeExample %}
{% endfunction %}

{% function 'string.to-lower-case($string)', 'to-lower-case($string)', 'returns:string' %}
  返回将 `$string` 中的 [ASCII][] 字母转换为小写的副本。

  [ASCII]: https://en.wikipedia.org/wiki/ASCII

  {% codeExample 'to-lower-case' %}
    @use "sass:string";

    @debug string.to-lower-case("Bold"); // "bold"
    @debug string.to-lower-case(SANS-SERIF); // sans-serif
    ===
    @use "sass:string"

    @debug string.to-lower-case("Bold")  // "bold"
    @debug string.to-lower-case(SANS-SERIF)  // sans-serif
  {% endcodeExample %}
{% endfunction %}

{% function 'string.unique-id()', 'unique-id()', 'returns:string' %}
  返回一个随机生成的未带引号的字符串，该字符串保证是一个有效的 CSS 标识符，并且在当前的 Sass 编译中是唯一的。

  {% codeExample 'unique-id' %}
    @use "sass:string";

    @debug string.unique-id(); // uabtrnzug
    @debug string.unique-id(); // u6w1b1def
    ===
    @use "sass:string"

    @debug string.unique-id(); // uabtrnzug
    @debug string.unique-id(); // u6w1b1def
  {% endcodeExample %}
{% endfunction %}

{% function 'string.unquote($string)', 'unquote($string)', 'returns:string' %}
  返回 `$string` 作为未带引号的字符串。这可能会生成无效的 CSS 字符串，因此请谨慎使用。

  {% codeExample 'unquote' %}
    @use "sass:string";

    @debug string.unquote("Helvetica"); // Helvetica
    @debug string.unquote(".widget:hover"); // .widget:hover
    ===
    @use "sass:string"

    @debug string.unquote("Helvetica")  // Helvetica
    @debug string.unquote(".widget:hover")  // .widget:hover
  {% endcodeExample %}
{% endfunction %}