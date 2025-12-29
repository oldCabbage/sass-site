---
title: '重大变更: -moz-document'
introduction: >
  Firefox 曾经支持一个需要特殊解析的 @-moz-document 规则。随着 Firefox 移除对它们的支持，Sass 也在逐步移除对这些规则的解析支持。
---

Sass 历史上支持对 `@-moz-document` 规则进行特殊解析。
由于 [Firefox 已经停止支持它们]，Sass 也将停止对这种特殊解析的支持，并将其视为未知的 at-rule。

[Firefox 已经停止支持它们]: https://web.archive.org/web/20200528221656/https://www.fxsitecompat.dev/en-CA/docs/2018/moz-document-support-has-been-dropped-except-for-empty-url-prefix/

**有一个例外**：仍然允许使用空 url 前缀函数，因为这用于针对 Firefox 的 hack。

{% codeExample 'moz-document' %}
  @-moz-document url-prefix() {
    .error {
      color: red;
    }
  }
  ===
  @-moz-document url-prefix()
    .error
      color: red
{% endcodeExample %}

## 过渡期

{% compatibility 'dart: "1.7.2"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

首先，我们将对所有 `@-moz-document` 的用法发出弃用警告，除了空 url 前缀 hack。

在 Dart Sass 2.0 中，`@-moz-document` 将被视为未知的 at-rule。

{% render 'silencing_deprecations' %}