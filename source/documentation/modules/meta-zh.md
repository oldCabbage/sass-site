---
title: sass:meta
---

{% render 'doc_snippets/built-in-module-status' %}

## 混合宏

{% function 'meta.apply($mixin, $args...)' %}
  {% compatibility 'dart: "1.69.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  使用 `$args` 包含 `$mixin`。如果传递了一个 [`@content` 块]，则将其转发到 `$mixin`。

  [`@content` 块]: /documentation/at-rules/mixin#content-blocks

  `$mixin` 必须是一个 [混合宏值]，例如由 [`meta.get-mixin()`] 返回的值。

  [混合宏值]: /documentation/values/mixins
  [`meta.get-mixin()`]: #get-mixin

  {% render 'code_snippets/example-first-class-mixin' %}
{% endfunction %}

{% function 'meta.load-css($url, $with: null)' %}
  {% compatibility 'dart: "1.23.0"', 'libsass: false', 'ruby: false' %}
    目前只有 Dart Sass 支持此混合宏。
  {% endcompatibility %}

  加载位于 `$url` 的 [模块]，并将其 CSS 包含进来，如同它是此混合宏的内容一样。`$with` 参数为模块提供 [配置]；如果传递了该参数，它必须是一个从变量名（不带 `$`）到在加载的模块中使用的变量值的映射。

  [模块]: /documentation/at-rules/use
  [配置]: /documentation/at-rules/use#configuration

  如果 `$url` 是相对路径，则被视为相对于包含 `meta.load-css()` 的文件的路径。

  **与 [`@use` 规则] 相比**：

  [`@use` 规则]: /documentation/at-rules/use

  * 即使以不同的方式加载多次，也只会评估给定的模块一次。

  * 无法为已经加载的模块提供配置，无论是否已经使用配置加载过它。

  **与 [`@use` 规则] 不同**：

  * [`@use` 规则]: /documentation/at-rules/use

  * 这不会使加载的模块中的任何成员在当前模块中可用。

  * 这可以在样式表的任何地方使用。甚至可以嵌套在样式规则中以创建嵌套样式！

  * 加载的模块 URL 可以来自变量并包含 [插值]。

    [插值]: /documentation/interpolation

  {% headsUp %}
    `$url` 参数应为一个包含传递给 `@use` 规则的 URL 的字符串。它不应是 CSS `url()`！
  {% endheadsUp %}

  {% codeExample 'load-css', false %}
    // dark-theme/_code.scss
    $border-contrast: false !default;

    code {
      background-color: #6b717f;
      color: #d2e1dd;
      @if $border-contrast {
        border-color: #dadbdf;
      }
    }
    ---
    // style.scss
    @use "sass:meta";

    body.dark {
      @include meta.load-css("dark-theme/code",
          $with: ("border-contrast": true));
    }
    ===
    // dark-theme/_code.sass
    $border-contrast: false !default

    code
      background-color: #6b717f
      color: #d2e1dd
      @if $border-contrast
        border-color: #dadbdf
    ---
    // style.sass
    @use "sass:meta"

    body.dark
      $configuration: ("border-contrast": true)
      @include meta.load-css("dark-theme/code", $with: $configuration)
    ===
    body.dark code {
      background-color: #6b717f;
      color: #d2e1dd;
      border-color: #dadbdf;
    }
  {% endcodeExample %}
{% endfunction %}

## 函数

{% function 'meta.accepts-content($mixin)', 'returns:boolean' %}
  {% compatibility 'dart: "1.69.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回给定的 [混合宏值] 是否可以接受一个 [`@content` 块]。

  [混合宏值]: /documentation/values/mixins
  [`@content` 块]: /documentation/at-rules/mixin#content-blocks

  即使混合宏不一定总是接受 `@content` 块，只要有可能接受，此函数也会返回 true。
{% endfunction %}

{% function 'meta.calc-args($calc)', 'returns:list' %}
  {% compatibility 'dart: "1.40.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回给定 [计算] 的参数。

  [计算]: /documentation/values/calculations

  如果参数是一个数字或嵌套计算，则返回该类型；否则，返回一个非引号字符串。

  {% codeExample 'calc-args' %}
    @use 'sass:meta';

    @debug meta.calc-args(calc(100px + 10%)); // unquote("100px + 10%")
    @debug meta.calc-args(clamp(50px, var(--width), 1000px)); // 50px, unquote("var(--width)"), 1000px
    ===
    @use 'sass:meta'

    @debug meta.calc-args(calc(100px + 10%))  // unquote("100px + 10%")
    @debug meta.calc-args(clamp(50px, var(--width), 1000px))  // 50px, unquote("var(--width)"), 1000px
  {% endcodeExample %}
{% endfunction %}

{% function 'meta.calc-name($calc)', 'returns:quoted string' %}
  {% compatibility 'dart: "1.40.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回给定 [计算] 的名称。

  [计算]: /documentation/values/calculations

  {% codeExample 'calc-name' %}
    @use 'sass:meta';

    @debug meta.calc-name(calc(100px + 10%)); // "calc"
    @debug meta.calc-name(clamp(50px, var(--width), 1000px)); // "clamp"
    ===
    @use 'sass:meta'

    @debug meta.calc-name(calc(100px + 10%))  // "calc"
    @debug meta.calc-name(clamp(50px, var(--width), 1000px))  // "clamp"
  {% endcodeExample %}
{% endfunction %}

{% function 'meta.call($function, $args...)', 'call($function, $args...)' %}
  {% render 'doc_snippets/call-impl-status' %}

  使用 `$args` 调用 `$function` 并返回结果。

  `$function` 必须是一个 [函数值]，例如由 [`meta.get-function()`] 返回的值。

  [函数值]: /documentation/values/functions
  [`meta.get-function()`]: #get-function

  {% render 'code_snippets/example-first-class-function' %}
{% endfunction %}

{% function 'meta.content-exists()', 'content-exists()', 'returns:boolean' %}
  返回当前混合宏是否传递了一个 [`@content` 块]。

  [`@content` 块]: /documentation/at-rules/mixin#content-blocks

  如果在混合宏之外调用，则会抛出错误。

  {% codeExample 'content-exists' %}
    @use 'sass:meta';

    @mixin debug-content-exists {
      @debug meta.content-exists();
      @content;
    }

    @include debug-content-exists; // false
    @include debug-content-exists { // true
      // 内容!
    }
    ===
    @use 'sass:meta'

    @mixin debug-content-exists
      @debug meta.content-exists()
      @content


    @include debug-content-exists  // false
    @include debug-content-exists   // true
      // 内容!
  {% endcodeExample %}
{% endfunction %}

{% function 'meta.feature-exists($feature)', 'feature-exists($feature)', 'returns:boolean' %}
  返回当前 Sass 实现是否支持 `$feature`。

  `$feature` 必须是一个字符串。当前识别的功能包括：

  * `global-variable-shadowing`，这意味着除非具有 `!global` 标志，局部变量将 [遮蔽] 全局变量。
  * `extend-selector-pseudoclass`，这意味着 [`@extend` 规则] 将影响像 `:not()` 这样的伪类中的嵌套选择器。
  * `units-level3`，这意味着 [单位运算] 支持 [CSS Values and Units Level 3] 中定义的单位。
  * `at-error`，这意味着支持 [`@error` 规则]。
  * `custom-property`，这意味着 [自定义属性声明] 的值不支持任何 [表达式] 除了 [插值]。

  [遮蔽]: /documentation/variables#shadowing
  [`@extend` 规则]: /documentation/at-rules/extend
  [单位运算]: /documentation/values/numbers#units
  [CSS Values and Units Level 3]: http://www.w3.org/TR/css3-values
  [`@error` 规则]: /documentation/at-rules/error
  [自定义属性声明]: /documentation/style-rules/declarations#custom-properties
  [表达式]: /documentation/syntax/structure#expressions
  [插值]: /documentation/interpolation

  对于任何未识别的 `$feature`，返回 `false`。
  
  {% headsUp %}
    此函数已弃用，应避免使用。有关详细信息，请参见 [重大更改页面]。

    [重大更改页面]: /documentation/breaking-changes/feature-exists
  {% endheadsUp %}

  {% codeExample 'feature-exists' %}
    @use "sass:meta";

    @debug meta.feature-exists("at-error"); // true
    @debug meta.feature-exists("unrecognized"); // false
    ===
    @use "sass:meta"

    @debug meta.feature-exists("at-error")  // true
    @debug meta.feature-exists("unrecognized")  // false
  {% endcodeExample %}
{% endfunction %}

{% function 'meta.function-exists($name, $module: null)', 'function-exists($name)', 'returns:boolean' %}
  返回名为 `$name` 的函数是否已定义，无论是内置函数还是用户定义的函数。

  如果传递了 `$module`，此函数还会检查命名空间为 `$module` 的模块中是否存在该函数定义。`$module` 必须是一个与当前文件中的 [`@use` 规则] 的命名空间匹配的字符串。

  [`@use` 规则]: /documentation/at-rules/use

  {% codeExample 'function-exists' %}
    @use "sass:meta";
    @use "sass:math";

    @debug meta.function-exists("div", "math"); // true
    @debug meta.function-exists("scale-color"); // true
    @debug meta.function-exists("add"); // false

    @function add($num1, $num2) {
      @return $num1 + $num2;
    }
    @debug meta.function-exists("add"); // true
    ===
    @use "sass:meta"
    @use "sass:math"

    @debug meta.function-exists("div", "math")  // true
    @debug meta.function-exists("scale-color")  // true
    @debug meta.function-exists("add")  // false

    @function add($num1, $num2)
      @return $num1 + $num2

    @debug meta.function-exists("add")  // true
  {% endcodeExample %}
{% endfunction %}

{% function 'meta.get-function($name, $css: false, $module: null)', 'get-function($name, $css: false, $module: null)', 'returns:function' %}
  返回名为 `$name` 的 [函数值]。

  [函数值]: /documentation/values/functions

  如果 `$module` 为 `null`，则返回命名空间（包括 [全局内置函数]）中名为 `$name` 的函数。否则，`$module` 必须是一个与当前文件中的 [`@use` 规则] 的命名空间匹配的字符串，在这种情况下，返回该模块中名为 `$name` 的函数。

  [全局内置函数]: /documentation/modules#global-functions
  [`@use` 规则]: /documentation/at-rules/use

  默认情况下，如果 `$name` 不是指 Sass 函数，则会抛出错误。但是，如果 `$css` 为 `true`，则返回一个 [纯 CSS 函数]。

  [纯 CSS 函数]: /documentation/at-rules/function/#plain-css-functions

  返回的函数可以使用 [`meta.call()`](#call) 调用。

  {% render 'code_snippets/example-first-class-function' %}
{% endfunction %}

{% function 'meta.get-mixin($name, $module: null)', 'returns:function' %}
  {% compatibility 'dart: "1.69.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

  返回名为 `$name` 的 [混合宏值]。

  [混合宏值]: /documentation/values/mixins

  如果 `$module` 为 `null`，则返回当前模块中定义的名为 `$name` 的混合宏。否则，`$module` 必须是一个与当前文件中的 [`@use` 规则] 的命名空间匹配的字符串，在这种情况下，返回该模块中名为 `$name` 的混合宏。

  [`@use` 规则]: /documentation/at-rules/use

  默认情况下，如果 `$name` 不是指混合宏，则会抛出错误。

  返回的混合宏可以使用 [`meta.apply()`](#apply) 包含。

  {% render 'code_snippets/example-first-class-mixin' %}
{% endfunction %}

{% function 'meta.global-variable-exists($name, $module: null)', 'global-variable-exists($name, $module: null)', 'returns:boolean' %}
  返回是否存在名为 `$name`（不带 `$`）的 [全局变量]。

  [全局变量]: /documentation/variables#scope

  如果 `$module` 为 `null`，则返回是否存在没有命名空间的名为 `$name` 的变量。否则，`$module` 必须是一个与当前文件中的 [`@use` 规则] 的命名空间匹配的字符串，在这种情况下，返回该模块中是否存在名为 `$name` 的变量。

  [`@use` 规则]: /documentation/at-rules/use

  参见 [`meta.variable-exists()`](#variable-exists)。

  {% codeExample 'global-variable-exists' %}
    @use "sass:meta";

    @debug meta.global-variable-exists("var1"); // false

    $var1: value;
    @debug meta.global-variable-exists("var1"); // true

    h1 {
      // $var2 是局部的。
      $var2: value;
      @debug meta.global-variable-exists("var2"); // false
    }
    ===
    @use "sass:meta"

    @debug meta.global-variable-exists("var1")  // false

    $var1: value
    @debug meta.global-variable-exists("var1")  // true

    h1
      // $var2 是局部的。
      $var2: value
      @debug meta.global-variable-exists("var2")  // false
  {% endcodeExample %}
{% endfunction %}

{% function 'meta.inspect($value)', 'inspect($value)', 'returns:unquoted string' %}
  返回 `$value` 的字符串表示。

  返回任何 Sass 值的表示，而不仅仅是可以在 CSS 中表示的值。因此，其返回值不一定有效 CSS。

  {% headsUp %}
    此函数用于调试；其输出格式不受 Sass 版本或实现的保证。
  {% endheadsUp %}

  {% codeExample 'inspect' %}
    @use "sass:meta";

    @debug meta.inspect(10px 20px 30px); // unquote("10px 20px 30px")
    @debug meta.inspect(("width": 200px)); // unquote('("width": 200px)')
    @debug meta.inspect(null); // unquote("null")
    @debug meta.inspect("Helvetica"); // unquote('"Helvetica"')
    ===
    @use "sass:meta"

    @debug meta.inspect(10px 20px 30px)  // unquote("10px 20px 30px")
    @debug meta.inspect(("width": 200px))  // unquote('("width": 200px)')
    @debug meta.inspect(null)  // unquote("null")
    @debug meta.inspect("Helvetica")  // unquote('"Helvetica"')
  {% endcodeExample %}
{% endfunction %}

{% function 'meta.keywords($args)', 'keywords($args)', 'returns:map' %}
  返回一个接受 [任意参数] 的混合宏或函数传递的关键字。`$args` 参数必须是一个 [参数列表]。

  [任意参数]: /documentation/at-rules/mixin#taking-arbitrary-arguments
  [参数列表]: /documentation/values/lists#argument-lists

  关键字作为从不带 `$` 的参数名称（作为非引号字符串）到这些参数值的映射返回。

  {% render 'code_snippets/example-mixin-arbitrary-keyword-arguments' %}
{% endfunction %}

{% function 'meta.mixin-exists($name, $module: null)', 'mixin-exists($name, $module: null)', 'returns:boolean' %}
  返回是否存在名为 `$name` 的 [混合宏]。

  [混合宏]: /documentation/at-rules/mixin

  如果 `$module` 为 `null`，则返回是否存在没有命名空间的名为 `$name` 的混合宏。否则，`$module` 必须是一个与当前文件中的 [`@use` 规则] 的命名空间匹配的字符串，在这种情况下，返回该模块中是否存在