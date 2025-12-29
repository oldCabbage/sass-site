---
title: '重大变更: 混合声明'
introduction: >
  CSS 改变了其处理混合嵌套规则和声明的方式，我们确保 Sass 的行为与之匹配。
---

## 到目前为止的故事

历史上，如果你在 Sass 中混合使用嵌套规则和声明，它会将所有声明移到规则的开头，以避免不必要的重复外部选择器。例如：

{% codeExample 'mixed-declarations-old' %}
  .example {
    color: red;

    &--serious {
      font-weight: bold;
    }

    font-weight: normal;
  }
  ===
  .example
    color: red

    &--serious
      font-weight: bold


    font-weight: normal
  ===
  .example {
    color: red;
    font-weight: normal;
  }

  .example--serious {
    font-weight: bold;
  }
{% endcodeExample %}

当 [纯 CSS 嵌套] 首次引入时，它表现相同。然而，在仔细考虑后，[CSS 工作组决定] 更合理的方式是让声明按照在文档中出现的顺序应用，如下所示：

[纯 CSS 嵌套]: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_nesting
[CSS 工作组决定]: https://github.com/w3c/csswg-drafts/issues/8738

{% codeExample 'mixed-declarations-new' %}
  .example {
    color: red;

    &--serious {
      font-weight: bold;
    }

    font-weight: normal;
  }
  ===
  .example
    color: red

    &--serious
      font-weight: bold


    font-weight: normal
  ===
  .example {
    color: red;
  }

  .example--serious {
    font-weight: bold;
  }

  .example {
    font-weight: normal;
  }
{% endcodeExample %}

## 废弃旧的方式

{% compatibility 'dart: "1.77.7"', 'libsass: false', 'ruby: false' %}
{% endcompatibility %}

使用嵌套规则 _之后_ 的声明首先被弃用，以通知用户即将发生的更改并给他们时间使样式表与之兼容。

希望提前采用新 CSS 语义的用户可以将嵌套声明用 `& {}` 包裹起来：

{% codeExample 'mixed-declarations-opt-in' %}
  .example {
    color: red;

    &--serious {
      font-weight: bold;
    }

    & {
      font-weight: normal;
    }
  }
  ===
  .example
    color: red

    &--serious
      font-weight: bold


    &
      font-weight: normal
{% endcodeExample %}

{% render 'silencing_deprecations' %}

## 新的方式

{% compatibility 'dart: "1.92.0"', 'libsass: false', 'ruby: false' %}
{% endcompatibility %}

现代版本的 Sass 与纯 CSS 的行为相同：声明按照编写顺序发出，即使这涉及为了处理交错的规则而复制包含它们的规则。对于大声 (`/* */` 样式的) 注释和没有子节点的 at-rules 也是如此。