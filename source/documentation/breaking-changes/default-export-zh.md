---
title: "重大变更：默认导出"
introduction: |
  默认情况下，Node.js 允许通过语法 `import sass from 'sass'` 从 ECMAScript 模块加载 [CommonJS 模块]。此功能现在已弃用；ESM 用户应使用 `import * as sass from 'sass'` 代替。

  [CommonJS 模块]: https://nodejs.org/docs/latest/api/modules.html#modules-commonjs-modules
  [ECMAScript 模块]: https://nodejs.org/api/esm.html#modules-ecmascript-modules
---

历史上，Dart Sass 仅作为 CommonJS 模块提供。这意味着任何使用 Node.js 原生 ECMAScript 模块支持的项目都可以像它提供 [默认导出] 一样加载它：

[默认导出]: https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export#using_the_default_export

```js
import sass from 'sass'; // 不再这样做
```

这并不是 Sass 团队的初衷，也不符合随包提供的类型声明，但它 _确实_ 可以工作。我们决定在 Dart Sass 2.0.0 中移除对此功能的支持，并要求 ECMAScript 模块用户仅使用包的命名导出：

```js
import * as sass from 'sass'; // 这样做
```

## 过渡期

{% compatibility 'dart: "1.54.0"', 'libsass: false', 'ruby: false' %}{% endcompatibility %}

在 Dart Sass 2.0.0 之前，我们将继续支持用户加载 Sass 的默认导出。第一次访问默认导出上的任何属性时，它将向 `console.error()` 发出弃用警告。为了避免此错误，请使用 `import * as sass from 'sass'` 代替。