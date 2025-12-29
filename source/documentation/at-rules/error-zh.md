---
title: "@error"
introduction: >
  在编写接受参数的 [mixins](/documentation/at-rules/mixin) 和
  [functions](/documentation/at-rules/function) 时，你通常希望确保这些参数具有你的 API
  所期望的类型和格式。如果不符，用户需要得到通知，并且你的 mixin/function 需要停止运行。
---

Sass 通过 `@error` 规则使得这变得容易，该规则写成 `@error
<expression>`。它会打印 [expression][]（通常是一个字符串）的值，以及一个堆栈跟踪，指示当前 mixin 或函数是如何被调用的。一旦错误被打印出来，Sass 会停止编译样式表，并告知正在运行它的系统发生了错误。

[expression]: /documentation/syntax/structure#expressions

{% codeExample 'error', false %}
  @mixin reflexive-position($property, $value) {
    @if $property != left and $property != right {
      @error "Property #{$property} must be either left or right.";
    }

    $left-value: if(sass($property == right): initial; else: $value);
    $right-value: if(sass($property == right): $value; else: initial);

    left: $left-value;
    right: $right-value;
    [dir=rtl] & {
      left: $right-value;
      right: $left-value;
    }
  }

  .sidebar {
    @include reflexive-position(top, 12px);
    //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    // Error: Property top must be either left or right.
  }
  ===
  @mixin reflexive-position($property, $value)
    @if $property != left and $property != right
      @error "Property #{$property} must be either left or right."


    $left-value: if(sass($property == right): initial; else: $value)
    $right-value: if(sass($property == right): $value; else: initial)

    left: $left-value
    right: $right-value
    [dir=rtl] &
      left: $right-value
      right: $left-value



  .sidebar
    @include reflexive-position(top, 12px)
    //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    // Error: Property top must be either left or right.
{% endcodeExample %}

错误和堆栈跟踪的具体格式因实现而异，也可能取决于你的构建系统。这是在命令行中使用 Dart Sass 运行时的样子：

```
Error: "Property top must be either left or right."
  ╷
3 │     @error "Property #{$property} must be either left or right.";
  │     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  ╵
  example.scss 3:5   reflexive-position()
  example.scss 19:3  root stylesheet
```