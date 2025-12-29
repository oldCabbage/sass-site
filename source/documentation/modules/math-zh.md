---
title: sass:math
---

{% render 'doc_snippets/built-in-module-status' %}

## 变量

{% function 'math.$e' %}
  {% compatibility 'dart: "1.25.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  最接近的64位浮点数近似值 [数学常数
  *e*][]。

  [数学常数 *e*]: https://en.wikipedia.org/wiki/E_(mathematical_constant)

  {% codeExample 'math-e' %}
    @use 'sass:math';

    @debug math.$e; // 2.7182818285
    ===
    @use 'sass:math'

    @debug math.$e  // 2.7182818285
  {% endcodeExample %}
{% endfunction %}

{% function 'math.$epsilon' %}
  {% compatibility 'dart: "1.55.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  根据浮点数比较，1和大于1的最小64位浮点数之间的差值。由于Sass数字的[10位精度](/documentation/values/numbers)，在许多情况下这将显得为0。
{% endfunction %}

{% function 'math.$max-number' %}
  {% compatibility 'dart: "1.55.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  可以表示为64位浮点数的最大有限数。

  {% codeExample 'math-max-number' %}
    @use 'sass:math';

    @debug math.$max-number; // 1797693134862315700000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
    ===
    @use 'sass:math'

    @debug math.$max-number  // 1797693134862315700000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
  {% endcodeExample %}
{% endfunction %}

{% function 'math.$max-safe-integer' %}
  {% compatibility 'dart: "1.55.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  最大整数 `n` 使得 `n` 和 `n + 1` 都可以精确表示为64位浮点数。

  {% codeExample 'math-max-safe-integer' %}
    @use 'sass:math';

    @debug math.$max-safe-integer; // 9007199254740991
    ===
    @use 'sass:math'

    @debug math.$max-safe-integer  // 9007199254740991
  {% endcodeExample %}
{% endfunction %}

{% function 'math.$min-number' %}
  {% compatibility 'dart: "1.55.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  可以表示为64位浮点数的最小正数。由于Sass数字的[10位精度](/documentation/values/numbers)，在许多情况下这将显得为0。
{% endfunction %}

{% function 'math.$min-safe-integer' %}
  {% compatibility 'dart: "1.55.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  最小整数 `n` 使得 `n` 和 `n - 1` 都可以精确表示为64位浮点数。

  {% codeExample 'math-min-safe-integer' %}
    @use 'sass:math';

    @debug math.$min-safe-integer; // -9007199254740991
    ===
    @use 'sass:math'

    @debug math.$min-safe-integer  // -9007199254740991
  {% endcodeExample %}
{% endfunction %}

{% function 'math.$pi' %}
  {% compatibility 'dart: "1.25.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  最接近的64位浮点数近似值 [数学常数
  *π*][]。

  [数学常数 *π*]: https://en.wikipedia.org/wiki/Pi

  {% codeExample 'math-pi' %}
    @use 'sass:math';

    @debug math.$pi; // 3.1415926536
    ===
    @use 'sass:math'

    @debug math.$pi  // 3.1415926536
  {% endcodeExample %}
{% endfunction %}

## 限制函数

{% function 'math.ceil($number)', 'ceil($number)', 'returns:number' %}
  将 `$number` 向上舍入到下一个最高整数。

  {% codeExample 'math-ceil' %}
    @use 'sass:math';

    @debug math.ceil(4); // 4
    @debug math.ceil(4.2); // 5
    @debug math.ceil(4.9); // 5
    ===
    @use 'sass:math'

    @debug math.ceil(4)  // 4
    @debug math.ceil(4.2)  // 5
    @debug math.ceil(4.9)  // 5
  {% endcodeExample %}
{% endfunction %}

{% function 'math.clamp($min, $number, $max)', 'returns:number' %}
  {% compatibility 'dart: "1.25.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  将 `$number` 限制在 `$min` 和 `$max` 之间。如果 `$number` 小于 `$min` 则返回 `$min`，如果大于 `$max` 则返回 `$max`。

  `$min`，`$number` 和 `$max` 必须具有兼容的单位，或者全部无单位。

  {% codeExample 'math-clamp' %}
    @use 'sass:math';

    @debug math.clamp(-1, 0, 1); // 0
    @debug math.clamp(1px, -1px, 10px); // 1px
    @debug math.clamp(-1in, 1cm, 10mm); // 10mm
    ===
    @use 'sass:math'

    @debug math.clamp(-1, 0, 1) // 0
    @debug math.clamp(1px, -1px, 10px) // 1px
    @debug math.clamp(-1in, 1cm, 10mm) // 10mm
  {% endcodeExample %}
{% endfunction %}

{% function 'math.floor($number)', 'floor($number)', 'returns:number' %}
  将 `$number` 向下舍入到下一个最低整数。

  {% codeExample 'math-floor' %}
    @use 'sass:math';

    @debug math.floor(4); // 4
    @debug math.floor(4.2); // 4
    @debug math.floor(4.9); // 4
    ===
    @use 'sass:math'

    @debug math.floor(4)  // 4
    @debug math.floor(4.2)  // 4
    @debug math.floor(4.9)  // 4
  {% endcodeExample %}
{% endfunction %}

{% function 'math.max($number...)', 'max($number...)', 'returns:number' %}
  返回一个或多个数字中的最大值。

  {% codeExample 'math-max' %}
    @use 'sass:math';

    @debug math.max(1px, 4px); // 4px

    $widths: 50px, 30px, 100px;
    @debug math.max($widths...); // 100px
    ===
    @use 'sass:math'

    @debug math.max(1px, 4px)  // 4px

    $widths: 50px, 30px, 100px
    @debug math.max($widths...)  // 100px
  {% endcodeExample %}
{% endfunction %}

{% function 'math.min($number...)', 'min($number...)', 'returns:number' %}
  返回一个或多个数字中的最小值。

  {% codeExample 'math-min' %}
    @use 'sass:math';

    @debug math.min(1px, 4px); // 1px

    $widths: 50px, 30px, 100px;
    @debug math.min($widths...); // 30px
    ===
    @use 'sass:math'

    @debug math.min(1px, 4px)  // 1px

    $widths: 50px, 30px, 100px
    @debug math.min($widths...)  // 30px
  {% endcodeExample %}
{% endfunction %}

{% function 'math.round($number)', 'round($number)', 'returns:number' %}
  将 `$number` 四舍五入到最接近的整数。

  {% codeExample 'math-round' %}
    @use 'sass:math';

    @debug math.round(4); // 4
    @debug math.round(4.2); // 4
    @debug math.round(4.9); // 5
    ===
    @use 'sass:math'

    @debug math.round(4)  // 4
    @debug math.round(4.2)  // 4
    @debug math.round(4.9)  // 5
  {% endcodeExample %}
{% endfunction %}

## 距离函数

{% function 'math.abs($number)', 'abs($number)', 'returns:number' %}
  返回 `$number` 的 [绝对值][]。如果 `$number` 为负，则返回 `-$number`，如果 `$number` 为正，则返回 `$number` 本身。

  [绝对值]: https://en.wikipedia.org/wiki/Absolute_value

  {% codeExample 'math-abs' %}
    @use 'sass:math';

    @debug math.abs(10px); // 10px
    @debug math.abs(-10px); // 10px
    ===
    @use 'sass:math'

    @debug math.abs(10px) // 10px
    @debug math.abs(-10px) // 10px
  {% endcodeExample %}
{% endfunction %}

{% function 'math.hypot($number...)', 'returns:number' %}
  {% compatibility 'dart: "1.25.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回具有与每个 `$number` 相同分量的 *n*-维 [向量][] 的长度。例如，对于三个数字 *a*，*b* 和 *c*，这将返回 *a² + b² + c²* 的平方根。

  这些数字必须全部具有兼容的单位，或者全部无单位。并且由于数字的单位可能不同，因此输出采用第一个数字的单位。

  [向量]: https://en.wikipedia.org/wiki/Euclidean_vector

  {% codeExample 'math-hypot' %}
    @use 'sass:math';

    @debug math.hypot(3, 4); // 5

    $lengths: 1in, 10cm, 50px;
    @debug math.hypot($lengths...); // 4.0952775683in
    ===
    @use 'sass:math'

    @debug math.hypot(3, 4) // 5

    $lengths: 1in, 10cm, 50px
    @debug math.hypot($lengths...) // 4.0952775683in
  {% endcodeExample %}
{% endfunction %}

## 指数函数

{% function 'math.log($number, $base: null)', 'returns:number' %}
  {% compatibility 'dart: "1.25.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回 `$number` 关于 `$base` 的 [对数][]。如果 `$base` 为 `null`，则计算 [自然对数][]。

  `$number` 和 `$base` 必须无单位。

  [对数]: https://en.wikipedia.org/wiki/Logarithm
  [自然对数]: https://en.wikipedia.org/wiki/Natural_logarithm

  {% codeExample 'math-log' %}
    @use 'sass:math';

    @debug math.log(10); // 2.302585093
    @debug math.log(10, 10); // 1
    ===
    @use 'sass:math'

    @debug math.log(10) // 2.302585093
    @debug math.log(10, 10) // 1
  {% endcodeExample %}
{% endfunction %}

{% function 'math.pow($base, $exponent)', 'returns:number' %}
  {% compatibility 'dart: "1.25.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  将 `$base` [提升到 `$exponent` 的幂][]。

  `$base` 和 `$exponent` 必须无单位。

  [提升到 `$exponent` 的幂]: https://en.wikipedia.org/wiki/Exponentiation

  {% codeExample 'math-pow' %}
    @use 'sass:math';

    @debug math.pow(10, 2); // 100
    @debug math.pow(100, math.div(1, 3)); // 4.6415888336
    @debug math.pow(5, -2); // 0.04
    ===
    @use 'sass:math'

    @debug math.pow(10, 2) // 100
    @debug math.pow(100, math.div(1, 3)) // 4.6415888336
    @debug math.pow(5, -2) // 0.04
  {% endcodeExample %}
{% endfunction %}

{% function 'math.sqrt($number)', 'returns:number' %}
  {% compatibility 'dart: "1.25.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回 `$number` 的 [平方根][]。

  `$number` 必须无单位。

  [平方根]: https://en.wikipedia.org/wiki/Square_root

  {% codeExample 'math-sqrt' %}
    @use 'sass:math';

    @debug math.sqrt(100); // 10
    @debug math.sqrt(math.div(1, 3)); // 0.5773502692
    @debug math.sqrt(-1); // NaN
    ===
    @use 'sass:math'

    @debug math.sqrt(100) // 10
    @debug math.sqrt(math.div(1, 3)) // 0.5773502692
    @debug math.sqrt(-1) // NaN
  {% endcodeExample %}
{% endfunction %}

## 三角函数

{% function 'math.cos($number)', 'returns:number' %}
  {% compatibility 'dart: "1.25

    @debug math.unit(math.div(5px, 1s))  // "px/s"
  {% endcodeExample %}
{% endfunction %}

## 其他函数

{% function 'math.div($number1, $number2)', 'returns:number' %}
  {% compatibility 'dart: "1.33.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回 `$number1` 除以 `$number2` 的结果。

  两个数字中任何共享的单位将被抵消。`$number1` 中不在 `$number2` 中的单位将出现在返回值的分子中，而 `$number2` 中不在 `$number1` 中的单位将出现在其分母中。

  {% headsUp %}
    为了向后兼容，此函数返回与 [已弃用的 `/` 运算符] 完全相同的结果，包括用 `/` 字符连接两个字符串。然而，这种行为最终将被移除，不应在新的样式表中使用。

    [已弃用的 `/` 运算符]: /documentation/breaking-changes/slash-div
  {% endheadsUp %}

  {% codeExample 'math-div' %}
    @use 'sass:math';

    @debug math.div(1, 2); // 0.5
    @debug math.div(100px, 5px); // 20
    @debug math.div(100px, 5); // 20px
    @debug math.div(100px, 5s); // 20px/s
    ===
    @use 'sass:math'

    @debug math.div(1, 2)  // 0.5
    @debug math.div(100px, 5px)  // 20
    @debug math.div(100px, 5)  // 20px
    @debug math.div(100px, 5s)  // 20px/s
  {% endcodeExample %}
{% endfunction %}

{% function 'math.percentage($number)', 'percentage($number)', 'returns:number' %}
  将一个无单位的 `$number`（通常是 0 到 1 之间的十进制数）转换为百分比。

  {% funFact %}
    此函数等同于 `$number * 100%`。
  {% endfunFact %}

  {% codeExample 'math-percentage' %}
    @use 'sass:math';

    @debug math.percentage(0.2); // 20%
    @debug math.percentage(math.div(100px, 50px)); // 200%
    ===
    @use 'sass:math'

    @debug math.percentage(0.2)  // 20%
    @debug math.percentage(math.div(100px, 50px))  // 200%
  {% endcodeExample %}
{% endfunction %}

{% function 'math.random($limit: null)', 'random($limit: null)', 'returns:number' %}
  如果 `$limit` 为 `null`，则返回一个介于 0 和 1 之间的随机十进制数。

  {% codeExample 'math-random' %}
    @use 'sass:math';

    @debug math.random(); // 0.2821251858
    @debug math.random(); // 0.6221325814
    ===
    @use 'sass:math'

    @debug math.random()  // 0.2821251858
    @debug math.random()  // 0.6221325814
  {% endcodeExample %}

  * * *

  如果 `$limit` 是大于或等于 1 的数字，则返回一个介于 1 和 `$limit` 之间的随机整数。

  {% headsUp %}
    `random()` 会忽略 `$limit` 中的单位。[此行为已弃用]，`random($limit)` 将返回一个与 `$limit` 参数具有相同单位的随机整数。

    [此行为已弃用]: /documentation/breaking-changes/function-units

    {% codeExample 'math-random-warning' %}
      @use 'sass:math';

      @debug math.random(100px); // 42
      ===
      @use 'sass:math'

      @debug math.random(100px)  // 42
    {% endcodeExample %}
  {% endheadsUp %}

  {% codeExample 'math-random-limit' %}
    @use 'sass:math';

    @debug math.random(10); // 4
    @debug math.random(10000); // 5373
    ===
    @use 'sass:math'

    @debug math.random(10)  // 4
    @debug math.random(10000)  // 5373
  {% endcodeExample %}
{% endfunction %}