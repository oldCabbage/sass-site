---
title: "重大变更：遗留 JS API"
introduction: |
  Dart Sass 最初使用的是 Node Sass 所使用的 API，但在 Dart Sass 1.45.0 中，它被替换为一个全新的现代 API。遗留的 JS API 现在已被弃用，并将在 Dart Sass 2.0.0 中移除。
---

## 迁移用法

### 入口点

遗留的 JS API 有两个用于编译 Sass 的入口点：`render` 和 `renderSync`，它们接受一个包含 `file`（用于编译文件）或 `data`（用于编译字符串）的选项对象。现代 API 有四个：`compile` 和 `compileAsync` 用于编译文件，`compileString` 和 `compileStringAsync` 用于编译字符串。这些函数接受路径或源字符串作为第一个参数，然后将所有其他选项作为对象传递给第二个参数。与使用回调的 `render` 不同，`compileAsync` 和 `compileStringAsync` 返回一个 promise。

详见 [使用文档]。

[使用文档]: /documentation/js-api/#md:usage

### 导入器

在遗留 API 中，导入器由一个单独的函数组成，该函数接受依赖规则 URL 和包含样式表的 URL（以及异步导入器的 `done` 回调），并返回一个包含磁盘上的 `file` 路径或要加载的样式表 `contents` 的对象。

现代 API [`Importer`] 代替包含两个方法：`canonicalize`，它接受规则 URL 并返回该 URL 的规范形式；以及 `load`，它接受规范 URL 并返回一个包含加载的样式表内容的对象。这种拆分确保了相同的模块只加载一次，并且相对 URL 一致工作。异步导入器的这两个方法都返回 promise。

还有一个特殊的 [`FileImporter`]，它将所有加载重定向到磁盘上现有的文件，当从返回 `file` 而不是 `contents` 的遗留导入器迁移时应使用它。

[`Importer`]: /documentation/js-api/interfaces/Importer/
[`ImporterResult`]: /documentation/js-api/interfaces/ImporterResult/
[`FileImporter`]: /documentation/js-api/interfaces/FileImporter/

### 自定义函数

在遗留的 JS API 中，自定义函数为每个 Sass 参数提供单独的 JS 参数，并为异步自定义函数提供额外的 `done` 回调。在现代 API 中，自定义函数改为接受一个包含所有 Sass 参数的单个 JS 参数，异步自定义函数返回一个 promise。

现代 API 还使用了一个更强大的 [`Value`] 类，支持所有 Sass 值类型、类型断言以及轻松的映射和列表查找。

[`Value`]: /documentation/js-api/classes/Value/

### 打包工具

如果你使用的是调用 Sass API 的打包工具或其他工具而不是直接使用它，你可能需要更改传递给该工具的配置，以告知其使用现代 API。

Webpack 应该默认使用现代 API，但如果你收到警告，请将 `api` 设置为 `"modern"` 或 `"modern-compiler"`。
详见 [Webpack 的文档]。

Vite 6 默认使用现代 API。Vite 的先前版本仍然使用遗留 API，但从 Vite 5.4 开始，你可以通过将 `api` 设置为 `"modern"` 或 `"modern-compiler"` 来切换它。详见 [Vite 的文档]。

对于其他工具，请查阅它们的文档或问题跟踪器以获取有关支持现代 Sass API 的信息。

[Webpack 的文档]: https://webpack.js.org/loaders/sass-loader/#api
[Vite 的文档]: https://vitejs.dev/config/shared-options.html#css-preprocessoroptions

## 静音警告

虽然在 Dart Sass 1.45.0 中与现代 API 的发布一起，遗留的 JS API 被标记为已弃用，但我们从 Dart Sass 1.79.0 开始对使用它发出警告。如果你目前还无法迁移到现代 API，但希望暂时静音警告，可以将 `legacy-js-api` 传递给 `silenceDeprecations` 选项：

```js
const sass = require('sass');

const result = sass.renderSync({
  silenceDeprecations: ['legacy-js-api'],
  ...
});
```

这将暂时静音警告，但遗留 API 将在 Dart Sass 2.0.0 中完全移除，因此你仍然应该计划尽快迁移到它。