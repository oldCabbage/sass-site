---
title: "@warn"
introduction: >
  在编写 [mixins](/documentation/at-rules/mixin) 和
  [functions](/documentation/at-rules/function) 时，您可能希望阻止用户传递某些参数或某些值。他们可能传递了现在已弃用的旧参数，或者他们可能以不是特别优化的方式调用了您的 API。
---

`@warn` 规则就是为了这个目的设计的。它写成 `@warn <expression>`
并为用户打印 [expression][]（通常是字符串）的值，以及一个堆栈跟踪，指示当前 mixin 或函数是如何被调用的。不过，与 [`@error` 规则][] 不同，它不会完全停止 Sass。

[expression]: /documentation/syntax/structure#expressions
[`@error` 规则]: /documentation/at-rules/error

{% codeExample 'warn' %}
  $known-prefixes: webkit, moz, ms, o;

  @mixin prefix($property, $value, $prefixes) {
    @each $prefix in $prefixes {
      @if not index($known-prefixes, $prefix) {
        @warn "Unknown prefix #{$prefix}.";
      }

      -#{$prefix}-#{$property}: $value;
    }
    #{$property}: $value;
  }

  .tilt {
    // Oops, we typo'd "webkit" as "wekbit"!
    @include prefix(transform, rotate(15deg), wekbit ms);
  }
  ===
  $known-prefixes: webkit, moz, ms, o

  @mixin prefix($property, $value, $prefixes)
    @each $prefix in $prefixes
      @if not index($known-prefixes, $prefix)
        @warn "Unknown prefix #{$prefix}."


      -#{$prefix}-#{$property}: $value

    #{$property}: $value


  .tilt
    // Oops, we typo'd "webkit" as "wekbit"!
    @include prefix(transform, rotate(15deg), wekbit ms)
  ===
  .tilt {
    -wekbit-transform: rotate(15deg);
    -ms-transform: rotate(15deg);
    transform: rotate(15deg);
  }
{% endcodeExample %}

警告和堆栈跟踪的具体格式因实现而异。这是在 Dart Sass 中的样子：

```
Warning: Unknown prefix wekbit.
    example.scss 6:7   prefix()
    example.scss 16:3  root stylesheet
```