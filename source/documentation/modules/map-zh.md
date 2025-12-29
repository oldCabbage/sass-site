---
title: sass:map
---

{% render 'doc_snippets/built-in-module-status' %}

{% funFact %}
  Sass 库和设计系统倾向于共享和重写以嵌套映射（包含其他映射的映射）表示的配置。

  为了帮助你处理嵌套映射，一些映射函数支持深度操作。例如，如果你向 `map.get()` 传递多个键，它将按照这些键查找所需的嵌套映射：

  {% codeExample 'map' %}
    @use "sass:map";

    $config: (a: (b: (c: d)));
    @debug map.get($config, a, b, c); // d
    ===
    @use "sass:map"

    $config: (a: (b: (c: d)))
    @debug map.get($config, a, b, c) // d
  {% endcodeExample %}
{% endfunFact %}

{% function 'map.deep-merge($map1, $map2)', 'returns:map' %}
  {% compatibility 'dart: "1.27.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  与 [`map.merge()`](#merge) 相同，不同之处在于嵌套映射的值也会递归合并。

  {% codeExample 'map-deep-merge' %}
    @use "sass:map";

    $helvetica-light: (
      "weights": (
        "lightest": 100,
        "light": 300
      )
    );
    $helvetica-heavy: (
      "weights": (
        "medium": 500,
        "bold": 700
      )
    );

    @debug map.deep-merge($helvetica-light, $helvetica-heavy);
    // (
    //   "weights": (
    //     "lightest": 100,
    //     "light": 300,
    //     "medium": 500,
    //     "bold": 700
    //   )
    // )
    @debug map.merge($helvetica-light, $helvetica-heavy);
    // (
    //   "weights": (
    //     "medium: 500,
    //     "bold": 700
    //   )
    // )
    ===
    @use "sass:map"

    $helvetica-light: ("weights": ("lightest": 100, "light": 300))
    $helvetica-heavy: ("weights": ("medium": 500, "bold": 700))

    @debug map.deep-merge($helvetica-light, $helvetica-heavy)
    // (
    //   "weights": (
    //     "lightest": 100,
    //     "light": 300,
    //     "medium": 500,
    //     "bold": 700
    //   )
    // )
    @debug map.merge($helvetica-light, $helvetica-heavy);
    // (
    //   "weights": (
    //     "medium: 500,
    //     "bold": 700
    //   )
    // )
  {% endcodeExample %}
{% endfunction %}

{% function 'map.deep-remove($map, $key, $keys...)', 'returns:map' %}
  {% compatibility 'dart: "1.27.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  如果 `$keys` 为空，则返回一个不包含与 `$key` 关联值的 `$map` 副本。

  {% codeExample 'map-deep-remove' %}
    @use "sass:map";

    $font-weights: ("regular": 400, "medium": 500, "bold": 700);

    @debug map.deep-remove($font-weights, "regular");
    // ("medium": 500, "bold": 700)
    ===
    @use "sass:map"

    $font-weights: ("regular": 400, "medium": 500, "bold": 700)

    @debug map.deep-remove($font-weights, "regular")
    // ("medium": 500, "bold": 700)
  {% endcodeExample %}

  ---

  如果 `$keys` 不为空，则从左到右跟随键集（包括 `$key` 并排除 `$keys` 中的最后一个键）以找到要更新的目标嵌套映射。

  返回一个 `$map` 的副本，其中目标映射不包含与 `$keys` 中最后一个键关联的值。

  {% codeExample 'map-deep-remove-2' %}
    @use "sass:map";

    $fonts: (
      "Helvetica": (
        "weights": (
          "regular": 400,
          "medium": 500,
          "bold": 700
        )
      )
    );

    @debug map.deep-remove($fonts, "Helvetica", "weights", "regular");
    // (
    //   "Helvetica": (
    //     "weights: (
    //       "medium": 500,
    //       "bold": 700
    //     )
    //   )
    // )
    ===
    @use "sass:map"

    $fonts: ("Helvetica": ("weights": ("regular": 400, "medium": 500, "bold": 700)))

    @debug map.deep-remove($fonts, "Helvetica", "weights", "regular")
    // (
    //   "Helvetica": (
    //     "weights: (
    //       "medium": 500,
    //       "bold": 700
    //     )
    //   )
    // )
  {% endcodeExample %}
{% endfunction %}

{% function 'map.get($map, $key, $keys...)', 'map-get($map, $key, $keys...)' %}
  如果 `$keys` 为空，则返回 `$map` 中与 `$key` 关联的值。

  如果 `$map` 中没有与 `$key` 关联的值，则返回 [`null`][]。

  [`null`]: /documentation/values/null

  {% render 'code_snippets/example-map-get' %}

  ---

  {% compatibility 'dart: "1.27.0"', 'libsass: false', 'ruby: false' %}
    仅 Dart Sass 支持调用 `map.get()` 传递超过两个参数。
  {% endcompatibility %}

  如果 `$keys` 不为空，则从左到右跟随键集（包括 `$key` 并排除 `$keys` 中的最后一个键）以找到要搜索的目标嵌套映射。

  返回目标映射中与 `$keys` 中最后一个键关联的值。

  如果映射中没有与键关联的值，或者 `$keys` 中的任何键在映射中缺失或引用的值不是映射，则返回 [`null`][]。

  [`null`]: /documentation/values/null

  {% codeExample 'map-deep-remove-2' %}
    @use "sass:map";

    $fonts: (
      "Helvetica": (
        "weights": (
          "regular": 400,
          "medium": 500,
          "bold": 700
        )
      )
    );

    @debug map.get($fonts, "Helvetica", "weights", "regular"); // 400
    @debug map.get($fonts, "Helvetica", "colors"); // null
    ===
    @use "sass:map"

    $fonts: ("Helvetica": ("weights": ("regular": 400, "medium": 500, "bold": 700)))

    @debug map.get($fonts, "Helvetica", "weights", "regular") // 400
    @debug map.get($fonts, "Helvetica", "colors") // null
  {% endcodeExample %}
{% endfunction %}

{% function 'map.has-key($map, $key, $keys...)', 'map-has-key($map, $key, $keys...)', 'returns:boolean' %}
  如果 `$keys` 为空，则返回 `$map` 是否包含与 `$key` 关联的值。

  {% codeExample 'map-has-key' %}
    @use "sass:map";

    $font-weights: ("regular": 400, "medium": 500, "bold": 700);

    @debug map.has-key($font-weights, "regular"); // true
    @debug map.has-key($font-weights, "bolder"); // false
    ===
    @use "sass:map"

    $font-weights: ("regular": 400, "medium": 500, "bold": 700)

    @debug map.has-key($font-weights, "regular") // true
    @debug map.has-key($font-weights, "bolder") // false
  {% endcodeExample %}

  ---

  {% compatibility 'dart: "1.27.0"', 'libsass: false', 'ruby: false' %}
    仅 Dart Sass 支持调用 `map.has-key()` 传递超过两个参数。
  {% endcompatibility %}

  如果 `$keys` 不为空，则从左到右跟随键集（包括 `$key` 并排除 `$keys` 中的最后一个键）以找到要搜索的目标嵌套映射。

  如果目标映射包含与 `$keys` 中最后一个键关联的值，则返回 true。

  如果不包含，或者 `$keys` 中的任何键在映射中缺失或引用的值不是映射，则返回 false。

  {% codeExample 'map-has-key-2' %}
    @use "sass:map";

    $fonts: (
      "Helvetica": (
        "weights": (
          "regular": 400,
          "medium": 500,
          "bold": 700
        )
      )
    );

    @debug map.has-key($fonts, "Helvetica", "weights", "regular"); // true
    @debug map.has-key($fonts, "Helvetica", "colors"); // false
    ===
    @use "sass:map"

    $fonts: ("Helvetica": ("weights": ("regular": 400, "medium": 500, "bold": 700)))

    @debug map.has-key($fonts, "Helvetica", "weights", "regular") // true
    @debug map.has-key($fonts, "Helvetica", "colors") // false
  {% endcodeExample %}
{% endfunction %}

{% function 'map.keys($map)', 'map-keys($map)', 'returns:list' %}
  返回 `$map` 中所有键的逗号分隔列表。

  {% codeExample 'map-keys' %}
    @use "sass:map";

    $font-weights: ("regular": 400, "medium": 500, "bold": 700);

    @debug map.keys($font-weights); // "regular", "medium", "bold"
    ===
    @use "sass:map"

    $font-weights: ("regular": 400, "medium": 500, "bold": 700)

    @debug map.keys($font-weights)  // "regular", "medium", "bold"
  {% endcodeExample %}
{% endfunction %}

{% function 'map.merge($map1, $map2)', 'map-merge($map1, $map2)', 'map.merge($map1, $keys..., $map2)', 'map-merge($map1, $keys..., $map2)', 'returns:map' %}
  {% headsUp %}
    实际上，`map.merge($map1, $keys..., $map2)` 的参数传递方式为 `map.merge($map1, $args...)`。此处描述为 `$map1, $keys..., $map2` 仅用于说明。
  {% endheadsUp %}

  如果没有传递 `$keys`，则返回一个包含 `$map1` 和 `$map2` 中所有键和值的新映射。

  如果 `$map1` 和 `$map2` 都有相同的键，则 `$map2` 的值优先。

  返回映射中所有也出现在 `$map1` 中的键的顺序与 `$map1` 中的顺序相同。`$map2` 中的新键出现在映射的末尾。

  {% codeExample 'map-merge' %}
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

  ---

  {% compatibility 'dart: "1.27.0"', 'libsass: false', 'ruby: false' %}
    仅 Dart Sass 支持调用 `map.merge()` 传递超过两个参数。
  {% endcompatibility %}

  如果 `$keys` 不为空，则跟随 `$keys` 找到要合并的目标嵌套映射。如果 `$keys` 中的任何键在映射中缺失或引用的值不是映射，则将该键的值设置为空映射。

  返回 `$map1` 的副本，其中目标映射被替换为一个新映射，该映射包含目标映射和 `$map2` 中的所有键和值。

  {% codeExample 'map-merge-2' %}
    @use "sass:map";

    $fonts: (
      "Helvetica": (
        "weights": (
          "lightest": 100,
          "light": 300
        )
      )
    );
    $heavy-weights: ("medium": 500, "bold": 700);

    @debug map.merge($fonts, "Helvetica", "weights", $heavy-weights);
    // (
    //   "Helvetica": (
    //     "weights": (
    //       "lightest": 100,
    //       "light": 300,
    //       "medium": 500,
    //       "bold": 700
    //     )
    //   )
    // )
    ===
    @use "sass:map"

    $fonts: ("Helvetica": ("weights": ("lightest": 100, "light": 300)))
    $heavy-weights: ("medium": 500, "bold": 700)

    @debug map.merge($fonts, "Helvetica", "weights", $heavy-weights)
    // (
    //   "Helvetica": (
    //     "weights": (
    //       "lightest": 100,
    //       "light": 300,
    //       "medium": 500,
    //       "bold": 700
    //     )
    //   )
    // )
  {% endcodeExample %}
{% endfunction %}

{% function 'map.remove($map, $keys...)', 'map-remove($map, $keys...)', 'returns:map' %}
  返回一个 `$map` 的副本，其中不包含与 `$keys` 关联的任何值。

  如果 `$keys` 中的键在 `$map` 中没有关联的值，则忽略该键。

  {% codeExample 'map-remove' %}
    @use "sass:map";

    $font-weights: ("regular": 400, "medium": 500, "bold": 700);

    @debug map.remove($font-weights, "regular"); // ("medium": 500, "bold": 700)
    @debug map.remove($font-weights, "regular", "bold"); // ("medium": 500)
    @debug map.remove($font-weights, "bolder");
    // ("regular": 400, "medium": 500, "bold": 700)
    ===
    @use "sass:map"

    $font-weights: ("regular": 400, "medium": 500, "bold": 700)

    @debug map.remove($font-weights, "regular")  // ("medium": 500, "bold": 700)
    @debug map.remove($font-weights, "regular", "bold")  // ("medium": 500)
    @debug map.remove($font-weights, "bolder")
    // ("regular": 400, "medium": 500, "bold": 700)
  {% endcodeExample %}
{% endfunction %}

{% function 'map.set($map, $key, $value)', 'map.set($map, $keys..., $key, $value)', 'returns:map' %}
  {% headsUp %}
    实际上，`map.set($map, $keys..., $key, $value)` 的参数传递方式为 `map.set($map, $args...)`。此处描述为 `$map, $keys..., $key, $value` 仅用于说明。
  {% endheadsUp %}

  如果没有传递 `$keys`，则返回一个 `$map` 的副本，其中 `$key` 的值被设置为 `$value`。

  {% codeExample 'map-set' %}
    @use "sass:map";

    $font-weights: ("regular": 400, "medium": 500, "bold": 700);

    @debug map.set($font-weights, "regular", 300);
    // ("regular": 300, "medium": 500, "bold": 700)
    ===
    @use "sass:map"

    $font-weights: ("regular": 400, "medium": 500, "bold": 700)

    @debug map.set($font-weights, "regular", 300)
    // ("regular": 300, "medium": 