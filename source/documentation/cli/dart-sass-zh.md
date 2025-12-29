---
title: Dart Sass 命令行界面
table_of_contents: true
---

## 使用方法

Dart Sass 可执行文件可以通过两种模式之一调用。

### 一对一模式

```shellsession
sass <input.scss> [output.css]
```

一对一模式将单个输入文件（`input.scss`）编译到单个输出位置（`output.css`）。如果没有传递输出位置，编译后的 CSS 将打印到终端。

如果输入文件的扩展名为 `.scss`，则将其解析为 [SCSS][]；如果扩展名为 `.sass`，则解析为 [缩进语法][]；如果扩展名为 `.css`，则解析为 [纯 CSS][]。如果没有这些扩展名之一，或者它来自标准输入，则默认解析为 SCSS。这可以通过 [`--indented` 标志][] 控制。

[SCSS]: /documentation/syntax#scss
[缩进语法]: /documentation/syntax#the-indented-syntax
[纯 CSS]: /documentation/at-rules/import/#importing-css
[`--indented` 标志]: #indented

特殊字符串 `-` 可以作为输入文件传递，告诉 Sass 从 [标准输入][] 读取输入文件。除非传递了 [`--indented` 标志][]，否则 Sass 默认将其解析为 SCSS。

[标准输入]: https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)

### 多对多模式

{% compatibility 'dart: "1.4.0"' %}{% endcompatibility %}

```shellsession
sass [<input.scss>:<output.css>] [<input/>:<output/>]...
```

多对多模式将一个或多个输入文件编译到一个或多个输出文件。输入和输出之间用冒号分隔。它还可以将一个目录中的所有 Sass 文件编译为另一个目录中同名的 CSS 文件。

```shellsession
​# 将 style.scss 编译为 style.css。
$ sass style.scss:style.css

​# 将 light.scss 和 dark.scss 编译为 light.css 和 dark.css。
$ sass light.scss:light.css dark.scss:dark.css

​# 将 themes/ 目录中的所有 Sass 文件编译为 public/css/ 目录中的 CSS 文件。
$ sass themes:public/css
```

在编译整个目录时，Sass 会忽略 [部分文件][]，其名称以 `_` 开头。您可以使用部分文件来分离样式表，而不会创建大量不必要的输出文件。

[部分文件]: /documentation/at-rules/import/#partials

## 选项

### 输入和输出

这些选项控制 Sass 如何加载其输入文件以及如何生成输出文件。

#### `--stdin`

此标志是告诉 Sass 它应该从 [标准输入][] 读取输入文件的另一种方式。传递后，不能传递输入文件。

```shellsession
$ echo "h1 {font-size: 40px}" | sass --stdin h1.css
$ echo "h1 {font-size: 40px}" | sass --stdin
h1 {
  font-size: 40px;
}
```

`--stdin` 标志不能与 [多对多模式][] 一起使用。

[多对多模式]: #many-to-many-mode

#### `--indented`

此标志告诉 Sass 将输入文件解析为 [缩进语法][]。如果在 [多对多模式][] 中使用，所有输入文件将被解析为缩进语法，尽管它们 [使用][] 的文件的语法将按常规确定。相反，`--no-indented` 可以用来强制所有输入文件被解析为 [SCSS][]。

[使用]: /documentation/at-rules/use

`--indented` 标志主要用于输入文件来自 [标准输入][] 的情况，因此其语法不能自动确定。

```shellsession
$ echo -e 'h1\n  font-size: 40px' | sass --indented -
h1 {
  font-size: 40px;
}
```

#### `--load-path`

此选项（缩写为 `-I`）为 Sass 添加一个额外的 [加载路径][] 以查找样式表。可以多次传递以提供多个加载路径。较早的加载路径将优先于较晚的加载路径。

[加载路径]: /documentation/at-rules/use#load-paths

```shellsession
$ sass --load-path=node_modules/bootstrap/dist/css style.scss style.css
```

#### `--pkg-importer=node`

{% compatibility 'dart: "1.71.0"' %}{% endcompatibility %}

此选项（缩写为 `-p node`）将 [Node.js `pkg:` 导入器] 添加到加载路径的末尾，以便样式表可以使用 Node.js 模块解析算法加载依赖项。

[Node.js `pkg:` 导入器]: /documentation/at-rules/use#node-js-package-importer

将来可能会添加对其他内置 `pkg:` 导入器的支持。

```shellsession
$ sass --pkg-importer=node style.scss style.css
```

#### `--style`

此选项（缩写为 `-s`）控制生成的 CSS 的输出样式。Dart Sass 支持两种输出样式：

- `expanded`（默认）将每个选择器和声明写在单独的行上。
- `compressed` 删除尽可能多的额外字符，并将整个样式表写在一行上。

```shellsession
$ sass --style=expanded style.scss
h1 {
  font-size: 40px;
}

$ sass --style=compressed style.scss
h1{font-size:40px}
```

#### `--no-charset`

{% compatibility 'dart: "1.19.0"' %}{% endcompatibility %}

此标志告诉 Sass 永远不要发出 `@charset` 声明或 UTF-8 [字节顺序标记][]。默认情况下，或如果传递了 `--charset`，如果样式表包含任何非 ASCII 字符，Sass 将插入 `@charset` 声明（在扩展输出模式下）或字节顺序标记（在压缩输出模式下）。

[字节顺序标记]: https://en.wikipedia.org/wiki/Byte_order_mark#UTF-8

```shellsession
$ echo 'h1::before {content: "👭"}' | sass --no-charset
h1::before {
  content: "👭";
}

$ echo 'h1::before {content: "👭"}' | sass --charset
@charset "UTF-8";
h1::before {
  content: "👭";
}
```

#### `--error-css`

{% compatibility 'dart: "1.20.0"' %}{% endcompatibility %}

此标志告诉 Sass 在编译过程中发生错误时是否发出 CSS 文件。此 CSS 文件在注释中以及 `body::before` 的 `content` 属性中描述了错误，因此您可以在不切换回终端的情况下在浏览器中看到错误消息。

默认情况下，如果编译到至少一个磁盘文件（而不是标准输出），则启用错误 CSS。您可以显式传递 `--error-css` 以在编译到标准输出时启用它，或传递 `--no-error-css` 以在任何地方禁用它。禁用时，[`--update` 标志][] 和 [`--watch` 标志][] 将在发生错误时删除 CSS 文件。

[`--watch` 标志]: #watch

```shellsession
$ sass --error-css style.scss style.css
/* Error: Incompatible units em and px.
 *   ,
 * 1 | $width: 15px + 2em;
 *   |         ^^^^^^^^^^
 *   '
 *   test.scss 1:9  root stylesheet */

body::before {
  font-family: "Source Code Pro", "SF Mono", Monaco, Inconsolata, "Fira Mono",
      "Droid Sans Mono", monospace, monospace;
  white-space: pre;
  display: block;
  padding: 1em;
  margin-bottom: 1em;
  border-bottom: 2px solid black;
  content: "Error: Incompatible units em and px.\a   \2577 \a 1 \2502  $width: 15px + 2em;\a   \2502          ^^^^^^^^^^\a   \2575 \a   test.scss 1:9  root stylesheet";
}
Error: Incompatible units em and px.
  ╷
1 │ $width: 15px + 2em;
  │         ^^^^^^^^^^
  ╵
  test.scss 1:9  root stylesheet
```

#### `--update`

{% compatibility 'dart: "1.4.0"' %}{% endcompatibility %}

如果传递了 `--update` 标志，Sass 将仅编译依赖项自上次生成对应的 CSS 文件以来已修改的样式表。它还会在更新样式表时打印状态消息。

```shellsession
$ sass --update themes:public/css
Compiled themes/light.scss to public/css/light.css.
```

### 源映射

{% compatibility 'dart: "1.3.0"' %}{% endcompatibility %}

{% render 'doc_snippets/source-maps' %}

Dart Sass 默认为它生成的每个 CSS 文件生成源映射。

#### `--no-source-map`

如果传递了 `--no-source-map` 标志，Sass 不会生成任何源映射。它不能与其他源映射选项一起传递。

```shellsession
$ sass --no-source-map style.scss style.css
```

#### `--source-map-urls`

此选项控制 Sass 生成的源映射如何链接回生成的 CSS 的贡献的 Sass 文件。Dart Sass 支持两种类型的 URL：

- `relative`（默认）使用从源映射文件位置到 Sass 源文件位置的相对 URL。
- `absolute` 使用 Sass 源文件的绝对 [`file:` URL][]。请注意，绝对 URL 只能在编译 CSS 的同一台计算机上工作。

[`file:` URL]: https://en.wikipedia.org/wiki/File_URI_scheme

```shellsession
​# 生成类似 "../sass/style.scss" 的 URL。
$ sass --source-map-urls=relative sass/style.scss css/style.css

​# 生成类似 "file:///home/style-wiz/sassy-app/sass/style.scss" 的 URL。
$ sass --source-map-urls=absolute sass/style.scss css/style.css
```

#### `--embed-sources`

此标志告诉 Sass 将贡献给生成的 CSS 的 Sass 文件的整个内容嵌入到源映射中。这可能会生成非常大的源映射，但可以保证源在任何计算机上都可用，无论 CSS 如何提供服务。

```shellsession
$ sass --embed-sources sass/style.scss css.style.css
```

#### `--embed-source-map`

此标志告诉 Sass 将源映射文件的内容嵌入到生成的 CSS 中，而不是创建单独的文件并通过 CSS 链接到它。

```shellsession
$ sass --embed-source-map sass/style.scss css.style.css
```

### 其他选项

#### `--watch`

{% compatibility 'dart: "1.6.0"' %}{% endcompatibility %}

此标志（缩写为 `-w`）类似于 [`--update` 标志][]，但在完成第一轮编译后，Sass 保持打开状态，并在它们或它们的依赖项更改时继续编译样式表。

[`--update` 标志]: #update

Sass 仅监视您在命令行上直接传递的目录、传递的文件名的父目录以及加载路径。它不会根据文件的 `@import`/`@use`/`@forward` 规则监视其他目录。

```shellsession
$ sass --watch themes:public/css
Compiled themes/light.scss to public/css/light.css.

​# 然后当你编辑 themes/dark.scss...
Compiled themes/dark.scss to public/css/dark.css.
```

#### `--poll`

{% compatibility 'dart: "1.8.0"' %}{% endcompatibility %}

此标志只能与 `--watch` 一起传递，告诉 Sass 手动定期检查源文件的更改，而不是依赖操作系统通知它发生了更改。如果您在操作系统通知系统不起作用的远程驱动器上编辑 Sass，这可能是必要的。

```shellsession
$ sass --watch --poll themes:public/css
Compiled themes/light.scss to public/css/light.css.

​# 然后当你编辑 themes/dark.scss...
Compiled themes/dark.scss to public/css/dark.css.
```

#### `--stop-on-error`

{% compatibility 'dart: "1.8.0"' %}{% endcompatibility %}

此标志告诉 Sass 在检测到错误时立即停止编译，而不是尝试编译可能不包含错误的其他 Sass 文件。它主要用于 [多对多模式][]。

```shellsession
$ sass --stop-on-error themes:public/css
Error: Expected expression.
   ╷
42 │ h1 {font-face: }
   │                ^
   ╵
  themes/light.scss 42:16  root stylesheet
```

#### `--interactive`

{% compatibility 'dart: "1.5.0"' %}{% endcompatibility %}

此标志（缩写为 `-i`）告诉 Sass 以交互模式运行，在交互模式中，您可以编写 [SassScript 表达式][] 并查看其结果。交互模式还支持 [变量][] 和 [`@use` 规则][]。

[SassScript 表达式]: /documentation/syntax/structure#expressions
[变量]: /documentation/variables
[`@use` 规则]: /documentation/at-rules/use

```shellsession
$ sass --interactive
>> 1px + 1in
97px
>> @use "sass:map"
>> $map: ("width": 100px, "height": 70px)
("width": 100px, "height": 70px)
>> map.get($map, "width")
100px
```

#### `--color`

此标志（缩写为 `-c`）告诉 Sass 发出 [终端颜色][]，而相反的 `--no-color` 告诉它不要发出颜色。默认情况下，如果看起来它正在运行支持它们的终端，它会发出颜色。

[终端颜色]: https://en.wikipedia.org/wiki/ANSI_escape_code#Colors

<pre class="language-plaintext"><code>$ sass --color style.scss style.css
Error: Incompatible units em and px.
  <span style="color: blue">╷</span>
<span style="color: blue">1 │</span> $width: <span style="color: crimson">15px + 2em</span>
  <span style="color: blue">│</span>         <span style="color: crimson">^^^^^^^^^^</span>
  <span style="color: blue">╵</span>
  style.scss 1:9  root stylesheet

$ sass --no-color style.scss style.css
Error: Incompatible units em and px.
  ╷
1 │ $width: 15px + 2em
  │         ^^^^^^^^^^
  ╵
  style.scss 1:9  root stylesheet</code></pre>

#### `--no-unicode`

{% compatibility 'dart: "1.17.0"' %}{% endcompatibility %}

此标志告诉 Sass 仅在错误消息中向终端发出 ASCII 字符。默认情况下，或如果传递了 `--unicode`，Sass 将为这些消息发出非 ASCII 字符。此标志不影响 CSS 输出。

```shellsession
$ sass --no-unicode style.scss style.css
Error: Incompatible units em and px.
  ,
1 | $width: 15px + 2em;
  |         ^^^^^^^^^^
  '
  test.scss 1:9  root stylesheet

$ sass --unicode style.scss style.css
Error: Incompatible units em and px.
  ╷
1 │ $width: 15px + 2em;
  │         ^^^^^^^^^^
  ╵
  test.scss 1:9  root stylesheet
```

#### `--verbose`

此标志告诉 Sass 在编译时发出所有弃用警告。默认情况下，当使用已弃用的功能时，Sass 仅发出给定弃用类型的五个警告，并抑制任何额外的警告。

```shellsession
$ sass --verbose style.scss style.css
```

#### `--quiet`

此标志（缩写为 `-q`）告诉 Sass 在编译时不发出任何警告。默认情况下，Sass 在使用已弃用的功能或遇到 [`@warn` 规则][] 时发出警告。它还会抑制 [`@debug` 规则][]。

[`@warn` 规则]: /documentation/at-rules/warn
[`@debug` 规则]: /documentation/at-rules/debug

```shellsession
$ sass --quiet style.scss style.css
```

#### `--quiet-deps`

此标志告诉 Sass 不发出来自依赖项的弃用警告。它将通过 [加载路径] 传递的任何文件视为“依赖项”。此标志不影响 [`@warn` 规则] 或 [`@debug` 规则]。

```shellsession
$ sass --load-path=node_modules --quiet-deps style.scss style.css
```

#### `--fatal-deprecation`

{% compatibility 'dart: "1.59.0"' %}{% endcompatibility %}

此选项告诉 Sass 将特定类型的弃用警告视为错误。例如，此命令告诉 Sass 将 `/` 作为除法的弃用警告视为错误：

```shellsession
$ sass --fatal-deprecation=slash-div style.scss style.css
Error: Using / for division outside of calc() is deprecated and will be removed in Dart Sass 2.0.0.

Recommendation: math.div(4, 2) or calc(4 / 2)

More info and automated migrator: /documentation/breaking-changes/slash-div

This is only an error because you've set the slash-div deprecation to be fatal.
Remove this setting if you need to keep using this feature.
  ╷
1 │ a { b: (4/2); }
  │         ^^^
  ╵
  style.scss 1:9  root stylesheet
```

{% deprecations 'active' %}{% enddeprecations %}

或者，您可以传递一个 Dart Sass 版本，将该版本中存在的所有弃用视为错误。例如，`--fatal-deprecation=1.33.0` 将会将上述表格中包括 `slash-div` 在内的所有弃用视为错误，但将任何较新的弃用视为警告。

{% deprecations 'obsolete' %}{% enddeprecations %}

#### `--future-deprecation`

{% compatibility 'dart: "1.59