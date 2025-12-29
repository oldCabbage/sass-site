---
title: '重大变更：扩展复合选择器'
introduction: >
  LibSass 目前允许像 `.message.info` 这样的复合选择器进行 [扩展](/documentation/at-rules/extend)，但其扩展方式与 `@extend` 的预期工作方式不匹配。
---

{% compatibility 'dart: true', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

当一个选择器扩展另一个选择器时，Sass 会将所有匹配扩展器的元素视为也匹配被扩展的类。换句话说，如果你写 `.heads-up {@extend .info}`，它的工作方式就像你在 HTML 中将 `class="heads-up"` 替换为 `class="heads-up info"`。

按照这种逻辑，你期望 `.heads-up {@extend .message.info}` 的工作方式就像将 `class="heads-up"` 替换为 `class="heads-up info message"`。但在 LibSass 和 Ruby Sass 中，情况并非如此——它不会将 `.heads-up` 添加到每个具有 *`.info` 或 `.message`* 的选择器中，而是只添加到具有 *`.info.message`* 的选择器中。

{% codeExample 'extend-compound-bad', false %}
  // 这两个选择器都应该被扩展，但实际上没有。
  .message {
    border: 1px solid black;
  }
  .info {
    font-size: 1.5rem;
  }

  .heads-up {
    @extend .message.info;
  }
  ===
  // 这两个选择器都应该被扩展，但实际上没有。
  .message
    border: 1px solid black

  .info
    font-size: 1.5rem


  .heads-up
    @extend .message.info
{% endcodeExample %}

为了解决这个问题，避免更多的混淆，并保持实现的简洁和高效，Dart Sass 不支持扩展复合选择器，LibSass 的未来版本中将移除此功能。为了兼容性，用户应分别扩展每个简单选择器：

{% codeExample 'extend-compound-good' %}
  .message {
    border: 1px solid black;
  }
  .info {
    font-size: 1.5rem;
  }

  .heads-up {
    @extend .message, .info;
  }
  ===
  .message
    border: 1px solid black

  .info
    font-size: 1.5rem


  .heads-up
    @extend .message, .info
{% endcodeExample %}

{% headsUp %}
  因为 Sass 不知道 CSS 将要样式化的 HTML 的细节，任何 `@extend` 可能需要生成一些不会应用于你特定 HTML 的额外选择器。这在切换到不扩展复合选择器时尤其明显。

  大多数情况下，这些额外的选择器不会引起任何问题，只会向 gzip 压缩后的 CSS 添加几个额外的字节。但有些样式表可能更依赖于旧的行为。在这种情况下，我们建议用 [占位符选择器][] 替换复合选择器。

  [placeholder selector]: /documentation/style-rules/placeholder-selectors

  {% codeExample 'extend-compound-heads-up' %}
    // 使用 `%message-info, .message.info` 而不是仅使用 `.message.info`。
    %message-info, .message.info {
      border: 1px solid black;
      font-size: 1.5rem;
    }

    .heads-up {
      // 使用 `%message-info` 而不是 `.message.info`。
      @extend %message-info;
    }
    ===
    // 使用 `%message-info, .message.info` 而不是仅使用 `.message.info`。
    %message-info, .message.info
      border: 1px solid black
      font-size: 1.5rem


    .heads-up
      // 使用 `%message-info` 而不是 `.message.info`。
      @extend %message-info
  {% endcodeExample %}
{% endheadsUp %}