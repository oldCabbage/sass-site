---
title: Migrator
table_of_contents: true
introduction: >
  Sass migrator 自动更新您的 Sass 文件，帮助您迁移到语言的最新版本。每个命令迁移单个功能，以尽可能让您控制要更新的内容和时间。
---

## 使用方法

要使用 Sass migrator，请告诉它要运行的 [迁移][] 以及要迁移的 Sass 文件：

[迁移]: #migrations

```shellsession
sass-migrator <migration> <entrypoint.scss...>
```

默认情况下，迁移器只会更改您在命令行上显式传递的文件。传递 [`--migrate-deps` 选项][] 告诉迁移器还更改使用 [`@use 规则`][], [`@forward 规则`][] 或 [`@import 规则`][] 加载的所有样式表。如果您想进行一次测试运行以查看将要进行的更改而不实际保存它们，可以传递 <code>[--dry-run][] [--verbose][]</code>（或简写为 `-nv`）。

[`--migrate-deps` 选项]: #migrate-deps
[`@use 规则`]: /documentation/at-rules/use
[`@forward 规则`]: /documentation/at-rules/forward
[`@import 规则`]: /documentation/at-rules/import
[--dry-run]: #dry-run
[--verbose]: #verbose

{% render 'code_snippets/example-module-migrator' %}

## 安装

您可以从大多数可以安装 [Dart Sass](/dart-sass) 的地方安装 Sass migrator：

### 独立安装

您可以通过从 [GitHub][] 下载适用于您操作系统的包并在 [将其添加到您的 `PATH`][] 中来在 Windows、Mac 或 Linux 上安装 Sass migrator。

[from GitHub]: {{ releases['migrator'].url }}
[将其添加到您的 `PATH`]: https://katiek2.github.io/path-doc/

### npm

如果您使用 Node.js，您也可以通过运行以下命令使用 [npm][] 安装 Sass migrator：

[npm]: https://www.npmjs.com

```shellsession
npm install -g sass-migrator
```

### Chocolatey

如果您使用 [Chocolatey 包管理器][] for Windows，您可以通过运行以下命令安装 Sass migrator：

[Chocolatey 包管理器]: https://chocolatey.org

```shellsession
choco install sass-migrator
```

### Homebrew

如果您使用 [Homebrew 包管理器][] for Mac OS X，您可以通过运行以下命令安装 Dart Sass：

[Homebrew 包管理器]: https://brew.sh

```shellsession
brew install sass/sass/migrator
```

## 全局选项

这些选项适用于所有迁移器。

### `--migrate-deps`

此选项（缩写为 `-d`）告诉迁移器更改的不仅仅是显式传递给命令行的样式表，还包括它们使用 [`@use 规则`][], [`@forward 规则`][] 或 [`@import 规则`][] 依赖的任何样式表。

```shellsession
$ sass-migrator module --verbose style.scss
Migrating style.scss
$ sass-migrator module --verbose --migrate-deps style.scss
Migrating style.scss
Migrating _theme.scss
Migrating _fonts.scss
Migrating _grid.scss
```

{% headsUp %}
  [模块迁移器][] 假设任何使用 [`@use 规则`][] 或 [`@forward 规则`][] 依赖的样式表已经迁移到模块系统，因此即使传递了 `--migrate-deps` 选项，它也不会尝试迁移它们。

  [模块迁移器]: #module
  [`@use 规则`]: /documentation/at-rules/use
  [`@forward 规则`]: /documentation/at-rules/forward
{% endheadsUp %}

### `--load-path`

此选项（缩写为 `-I`）告诉迁移器在何处查找样式表的 [加载路径][]。它可以多次传递以提供多个加载路径。较早的加载路径优先于较晚的加载路径。

从加载路径加载的依赖项假定为第三方库，因此即使传递了 [`--migrate-deps` 选项][]，迁移器也不会迁移它们。

[加载路径]: /documentation/at-rules/use#load-paths

### `--dry-run`

此标志（缩写为 `-n`）告诉迁移器不要将任何更改保存到磁盘。相反，它会打印它本会更改的文件列表。这通常与 [`--verbose 选项`][] 一起使用，以打印本会进行的更改的内容。

[`--verbose 选项`]: #verbose

```shellsession
$ sass-migrator module --dry-run --migrate-deps style.scss
Dry run. Logging migrated files instead of overwriting...

style.scss
_theme.scss
_fonts.scss
_grid.scss
```

#### `--no-unicode`

此标志告诉 Sass migrator 仅在错误消息中向终端输出 ASCII 字符。默认情况下，或者如果传递了 `--unicode`，迁移器会为这些消息输出非 ASCII 字符。此标志不会影响 CSS 输出。

```shellsession
$ sass-migrator --no-unicode module style.scss
line 1, column 9 of style.scss: Error: Could not find Sass file at 'typography'.
  ,
1 | @import "typography";
  |         ^^^^^^^^^^^^
  '
Migration failed!
$ sass-migrator --unicode module style.scss
line 1, column 9 of style.scss: Error: Could not find Sass file at 'typography'.
  ╷
1 │ @import "typography";
  │         ^^^^^^^^^^^^
  ╵
Migration failed!
```

### `--verbose`

此标志（缩写为 `-v`）告诉迁移器在控制台打印额外信息。默认情况下，它仅打印被更改文件的名称，但与 [`--dry-run 选项`][] 结合使用时，它还会打印这些文件的新内容。

[`--dry-run 选项`]: #dry-run

```shellsession
$ sass-migrator module --verbose --dry-run style.scss
Dry run. Logging migrated files instead of overwriting...
<==> style.scss
@use "bootstrap" with (
  $body-bg: #000,
  $body-color: #111
);

@include bootstrap.media-breakpoint-up(sm) {
  .navbar {
    display: block;
  }
}
$ sass-migrator module --verbose style.scss
Migrating style.scss
```

## 迁移

### `if()`

此迁移将旧的 `if()` 函数转换为新的 CSS `if()` 语法。详情见 [/d/if-function]。

[/d/if-function]: /documentation/breaking-changes/if-function

### 颜色

此迁移将旧的颜色函数转换为新的颜色空间兼容函数。

### 除法

此迁移将使用 [`/` 作为除法] 的样式表更改为使用内置的 `math.div` 函数。

[`/` 作为除法]: /documentation/breaking-changes/slash-div

#### `--pessimistic`

默认情况下，迁移器即使不确定在评估时是否为除法也会将 `/` 操作转换为 `math.div`。只有当它可以静态确定它们在执行其他操作时（例如，没有 SassScript 参与，或其中一个操作数是字符串）才会保持原样。`math.div` 函数目前与 `/` 运算符功能相同，因此这样做是安全的，但可能会在 `math.div` 的某个参数在运行时不是数字时导致新的警告。

如果您想避免这种行为，可以传递 `--pessimistic` 标志。使用此标志时，迁移器只会转换它确定为执行除法的 `/` 操作。这将防止不必要的 `math.div` 转换，但如果无法静态确定，可能会留下一些未迁移的除法。

### 模块

此迁移将使用旧的 [`@import 规则`][] 加载依赖项的样式表更改为通过 [`@use 规则`][] 使用 Sass 模块系统。它不仅仅是简单地将 `@import` 更改为 `@use`，而是智能地更新样式表，使其继续以与以前相同的方式工作，包括：

- 为来自其他模块的成员（变量、混合宏和函数）的使用添加命名空间。

- 为使用成员而未导入它们的样式表添加新的 `@use` 规则。

- 将被覆盖的默认变量转换为 [`with 子句`][]。

  [`with 子句`]: /documentation/at-rules/use#configuration

- 自动从其他文件使用的成员中删除 `-` 和 `_` 前缀（否则它们将被视为 [私有成员][] 并且只能在声明它们的模块中使用）。

  [私有成员]: /documentation/at-rules/use#private-members

- 将 [嵌套导入][] 转换为使用 [`meta.load-css()` 混合宏][] 代替。

  [嵌套导入]: /documentation/at-rules/import/#nesting
  [`meta.load-css()` 混合宏]: /documentation/modules/meta#load-css

{% headsUp %}
  由于模块迁移器可能需要修改成员定义 _和_ 成员名称，因此重要的是要使用 [`--migrate-deps` 选项][] 运行它，或者确保传递给它包或应用程序中的所有样式表。

  [`--migrate-deps` 选项]: #migrate-deps
{% endheadsUp %}

{% render 'code_snippets/example-module-migrator' %}

#### 加载依赖项

模块迁移器需要能够读取它正在迁移的样式表所依赖的所有样式表，即使未传递 [`--migrate-deps` 选项][]。如果迁移器无法找到依赖项，您将收到错误。

```shellsession
$ ls .
style.scss  node_modules
$ sass-migrator module style.scss
Error: Could not find Sass file at 'dependency'.
  ,
1 | @import "dependency";
  |         ^^^^^^^^^^^^
  '
  style.scss 1:9  root stylesheet
Migration failed!
$ sass-migrator --load-path node_modules module style.scss
```

如果您在编译样式表时使用 [加载路径][]，请确保使用 [`--load-path 选项`][] 将其传递给迁移器。

不幸的是，迁移器不支持自定义导入器，但它内置支持通过在 `node_modules` 中搜索来解析以 `~` 开头的 URL，类似于 [Webpack 支持的那样][]。

[加载路径]: /documentation/at-rules/use#load-paths
[`--load-path 选项`]: #load-path
[Webpack 支持的那样]: https://github.com/webpack-contrib/sass-loader#resolving-import-at-rules

#### `--remove-prefix`

此选项（缩写为 `-p`）接受一个标识符前缀，当迁移时从所有变量、混合宏和函数名称的开头删除。不以该前缀开头的成员将保持不变。

[`@import 规则`][] 将所有顶级成员放在一个全局作用域中，因此当它是加载样式表的标准方式时，每个人都被激励为所有成员名称添加前缀以避免意外重新定义其他样式表的。模块系统解决了这个问题，因此现在这些前缀不再必要时自动剥离它们是有用的。

```shellsession
$ cat style.scss
@import "theme";

@mixin app-inverted {
  color: $app-bg-color;
  background-color: $app-color;
}
$ sass-migrator --migrate-deps module --remove-prefix=app- style.scss
$ cat style.scss
@use "theme";

@mixin inverted {
  color: theme.$bg-color;
  background-color: theme.$color;
}
```

当传递此选项时，迁移器还会生成一个 [仅导入样式表][]，该样式表 [转发][] 添加了前缀的所有成员，以保留使用该库的用户的向后兼容性。

[仅导入样式表]: /documentation/at-rules/import/#import-only-files
[转发]: /documentation/at-rules/forward

此选项可以多次传递，或用逗号分隔多个值。每个前缀将从具有该前缀的任何成员中删除。如果一个成员匹配多个前缀，将删除最长的匹配前缀。

#### `--forward`

此选项告诉迁移器使用 [`@forward 规则`][] 转发哪些成员。它支持以下设置：

- `none`（默认值）不转发任何成员。

- `all` 转发除了原始样式表中以 `-` 或 `_` 开头的成员之外的所有成员，因为这通常用于在引入模块系统之前标记包私有成员。

- `prefixed` 仅转发以传递给 [`--remove-prefix` 选项][] 的前缀开头的成员。此选项必须与 `--remove-prefix` 选项结合使用。

  [`--remove-prefix 选项`]: #remove-prefix

所有显式传递在命令行上的文件将转发这些文件使用 `@import` 规则传递性加载的成员。使用 [`--migrate-deps` 选项][] 加载的文件不会转发任何新成员。这对于迁移 Sass 库特别有用，因为它确保该库的用户仍然可以访问它定义的所有成员。

```shellsession
$ cat _index.scss
@import "theme";
@import "typography";
@import "components";
$ sass-migrator --migrate-deps module --forward=all style.scss
$ cat _index.scss
@forward "theme";
@forward "typography";
@forward "components";
```

### 命名空间

此迁移允许您轻松更改样式表中 `@use` 规则的 [命名空间][]。如果模块迁移器生成的解决冲突的命名空间不是理想的，或者您不想使用 Sass 根据规则的 URL 确定的默认命名空间，这很有用。

[命名空间]: /documentation/at-rules/use#choosing-a-namespace

#### `--rename`

您可以告诉迁移器要更改的命名空间（或命名空间）通过传递表达式给 `--rename` 选项。

这些表达式的格式为 `<旧命名空间> to <新命名空间>` 或 `url <规则 URL> to <新命名空间>`。在这些表达式中，`<旧命名空间>` 和 `<规则 URL>` 是 [正则表达式][]，分别匹配现有命名空间的全部内容或 `@use` 规则的 URL。

[正则表达式]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions

对于简单的用例，这看起来像 `--rename 'old to new'`，这将重命名命名空间为 `old` 的 `@use` 规则为 `new`。

但是，您也可以使用它来完成更复杂的重命名。例如，假设您之前有一个如下所示的样式表：

```scss
@import 'components/button/lib/mixins';
@import 'components/input/lib/mixins';
@import 'components/table/lib/mixins';
// ...
```

由于所有这些 URL 在迁移到 `@use` 规则时都会具有默认命名空间 `mixins`，模块迁移器可能会生成类似以下内容：

```scss
@use 'components/button/lib/mixins' as button-lib-mixins;
@use 'components/input/lib/mixins' as input-lib-mixins;
@use 'components/table/lib/mixins' as table-lib-mixins;
// ...
```

这在代码上是有效的，因为命名空间不会冲突，但它们比需要的要复杂得多。相关的部分是组件名称，因此我们可以使用命名空间迁移器提取该部分。

如果我们使用 `--rename 'url components/(\w+)/lib/mixins to \1'` 运行命名空间迁移器，我们将得到：

```scss
@use 'components/button/lib/mixins' as button;
@use 'components/input/lib/mixins' as input;
@use 'components/table/lib/mixins' as table;
// ...
```

这里的重命名脚本表示查找所有 URL 看起来像 `components/(\w+)/lib/mixins` 的 `@use` 规则（正则表达式中的 `\w+` 表示匹配一个或多个字符的单词）。输出子句中的 `\1` 表示将正则表达式中的第一组括号（称为 [组][]）的内容代入。

[group]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions/Groups_and_Ranges

如果您希望应用多个重命名，可以多次传递 `--rename` 选项，或用分号或换行符分隔它们。仅会使用适用于给定规则的第一个重命名，因此您可以传递类似 `--rename 'a to b; b to a'` 的内容来交换命名空间 `a` 和 `b`。

#### `--force`

默认情况下，如果迁移后两个或多个 `@use` 规则具有相同的命名空间，迁移器将失败，不会进行任何更改。

在这种情况下，您通常希望调整 `--rename` 脚本以避免创建冲突，但如果希望强制迁移，可以传递 `--force`。

使用 `--force`，如果遇到任何冲突，第一个 `@use` 规则将获取其首选命名空间，而后续具有相同首选命名空间的 `@use` 规则将附加一个数字后缀。