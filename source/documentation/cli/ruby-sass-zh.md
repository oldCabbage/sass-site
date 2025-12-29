---
title: Ruby Sass 命令行接口
table_of_contents: true
---

{% headsUp %}
  [Ruby Sass 已结束生命周期][] 并且现在完全不再维护。请尽快切换到 [Dart Sass][] 或 [LibSass][]。

  [Ruby Sass 已结束生命周期]: /blog/ruby-sass-is-unsupported
  [Dart Sass]: /dart-sass
  [LibSass]: /libsass
{% endheadsUp %}

## 使用方法

Ruby Sass 可执行文件可以以两种模式之一调用。

### 一对一模式

```shellsession
sass [input.scss] [output.css]
```

一对一模式将单个输入文件（`input.scss`）编译到单个输出位置（`output.css`）。如果没有传递输出位置，则编译后的 CSS 会打印到终端。如果没有传递输入 _或_ 输出，则从[标准输入][]读取 CSS 并打印到终端。

[标准输入]: https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)

如果输入文件的扩展名为 `.scss`，则将其解析为 [SCSS][]；如果扩展名为 `.sass`，则将其解析为 [缩进语法][]。如果没有这两种扩展名之一，或者来自标准输入，则默认解析为缩进语法。这可以通过 [`--scss` 标志][] 控制。

[SCSS]: /documentation/syntax#scss
[缩进语法]: /documentation/syntax#the-indented-syntax
[`--scss` 标志]: #scss

### 多对多模式

```shellsession
sass [<input.css>:<output.css>] [<input/>:<output/>] [input.css] [input/]...
```

多对多模式将一个或多个输入文件编译为一个或多个输出文件。输入与输出用冒号分隔。它还可以将一个目录中的所有 Sass 文件编译为另一个目录中具有相同名称的 CSS 文件。当任何参数包含冒号，_或_传递了 [`--update` 标志][] 或 [`--watch` 标志][] 时，将启用多对多模式。

[`--update` 标志]: #update
[`--watch` 标志]: #watch

如果传递了没有相应输出文件的输入文件，则将其编译为与输入文件同名但扩展名为 `.css` 的 CSS 文件。如果传递了没有相应输出目录的输入目录，则编译该目录中的所有 Sass 文件为相同的目录中的 CSS 文件。

```shellsession
$ sass style.scss:style.css
      write style.css
      write style.css.map
$ sass light.scss:light.css dark.scss:dark.css
      write light.css
      write light.css.map
      write dark.css
      write dark.css.map
$ sass themes:public/css
      write public/css/light.css
      write public/css/light.css.map
      write public/css/dark.css
      write public/css/dark.css.map
```

在编译整个目录时，Sass 将忽略名称以 `_` 开头的[部分文件][]。您可以使用部分文件将样式表分隔开，而不会创建一堆不必要的输出文件。

[部分文件]: /documentation/at-rules/use/#partials

多对多模式只会编译自上次生成相应 CSS 文件以来依赖项已修改的样式表。它还会在更新样式表时打印状态消息。

## 选项

### 常用

#### `--load-path`

此选项（缩写为 `-I`）为 Sass 添加一个额外的[加载路径][]，用于查找样式表。可以多次传递以提供多个加载路径。较早的加载路径优先于较晚的加载路径。

[加载路径]: /documentation/at-rules/use#load-paths

```shellsession
$ sass --load-path=node_modules/bootstrap/dist/css style.scss style.css
```

如果设置了 `SASS_PATH` [环境变量][]，则还会从该变量加载加载路径。该变量应为用 `;`（在 Windows 上）或 `:`（在其他操作系统上）分隔的路径列表。`SASS_PATH` 上的加载路径优先于命令行上传递的加载路径。

[环境变量]: https://en.wikipedia.org/wiki/Environment_variable

```shellsession
$ SASS_PATH=node_modules/bootstrap/dist/css sass style.scss style.css
```

#### `--require`

此选项（缩写为 `-r`）在运行 Sass 之前加载一个 [Ruby gem][]。可以用来将用 Ruby 定义的函数加载到 Sass 环境中。

[Ruby gem]: https://rubygems.org/

```shellsession
$ sass --require=rails-sass-images style.scss style.css
```

#### `--compass`

此标志加载 [Compass 框架][] 并使其混合宏和函数可用于 Sass。

[Compass 框架]: http://compass-style.org/

```shellsession
$ sass --compass style.scss style.css
```

#### `--style`

此选项（缩写为 `-t`）控制生成的 CSS 的输出样式。Ruby Sass 支持四种输出样式：

- `nested`（默认值）缩进 CSS 规则以匹配 Sass 源代码的嵌套。
- `expanded` 将每个选择器和声明写在单独的行上。
- `compact` 将每个 CSS 规则放在单独的一行上。
- `compressed` 尽可能删除多余的字符，并将整个样式表写在一行上。

```shellsession
$ sass --style=nested
h1 {
  font-size: 40px; }
  h1 code {
    font-face: Roboto Mono; }

$ sass --style=expanded style.scss
h1 {
  font-size: 40px;
}
h1 code {
  font-face: Roboto Mono;
}

$ sass --style=compact style.scss
h1 { font-size: 40px; }
h1 code { font-face: Roboto Mono; }

$ sass --style=compressed style.scss
h1{font-size:40px}h1 code{font-face:Roboto Mono}
```

#### `--help`

此标志（缩写为 `-h` 和 `-?`）打印此文档的摘要。

```shellsession
$ sass --help
Usage: sass [options] [INPUT] [OUTPUT]

Description:
  Converts SCSS 或 Sass files to CSS。

...
```

#### `--version`

此标志打印当前的 Sass 版本。

```shellsession
$ sass --version
Sass 3.7.4
```

### 监视和更新

这些选项影响[多对多模式][]。

[多对多模式]: #many-to-many-mode

#### `--watch`

启用[多对多模式][]，并使 Sass 保持打开状态并继续编译样式表，当它们或它们的依赖项发生变化时。

```shellsession
$ sass --watch themes:public/css
      write public/css/light.css
      write public/css/light.css.map

# 然后当你编辑 themes/dark.scss...
      write public/css/dark.css
      write public/css/dark.css.map
```

#### `--poll`

此标志只能与 `--watch` 一起传递，告诉 Sass 以一定间隔手动检查源文件的变化，而不是依赖操作系统通知它发生了变化。如果您在操作系统的通知系统不起作用的远程驱动器上编辑 Sass，则可能需要这样做。

```shellsession
$ sass --watch --poll themes:public/css
      write public/css/light.css
      write public/css/light.css.map

# 然后当你编辑 themes/dark.scss...
      write public/css/dark.css
      write public/css/dark.css.map
```

#### `--update`

此标志即使没有传递任何冒号分隔的对，也会启用[多对多模式][]。

```shellsession
$ sass --update style.scss
      write style.css
      write style.css.map
```

#### `--force`

此标志（缩写为 `-f`）只能在[多对多模式][]中传递。它会导致 Sass 文件_始终_被编译为 CSS 文件，而不是仅在源文件比输出文件更新时才编译。

`--force` 标志不能与 [`--watch` 标志][] 一起传递。

```shellsession
$ sass --force style.scss:style.css
      write style.css
      write style.css.map
```

#### `--stop-on-error`

此标志只能在[多对多模式][]中传递。它告诉 Sass 在检测到错误时立即停止编译，而不是尝试编译可能不包含错误的其他 Sass 文件。

```shellsession
$ sass --stop-on-error themes:public/css
Error: Invalid CSS after "h1 {font-size: ": expected expression (e.g. 1px, bold), was "}"
        on line 1 of test.scss
  Use --trace for backtrace.
```

### 输入和输出

这些选项控制 Sass 如何加载其输入文件以及如何生成输出文件。

#### `--scss`

此标志告诉 Sass 将[标准输入][]解析为 [SCSS][]。

```shellsession
$ echo "h1 {font-size: 40px}" | sass --scss
h1 {
  font-size: 40px;
}
```

#### `--sourcemap`

此选项控制 Sass 如何生成源映射，源映射是告诉浏览器或其他使用 CSS 的工具该 CSS 如何对应于生成它的 Sass 文件的文件。它们使您能够在浏览器中查看甚至编辑 Sass 文件成为可能。请参阅在 [Chrome][] 和 [Firefox][] 中使用源映射的说明。它有四个可能的值：

[Chrome]: https://developers.google.com/web/tools/chrome-devtools/javascript/source-maps
[Firefox]: https://developer.mozilla.org/en-US/docs/Tools/Style_Editor#Source_map_support

- `auto`（默认值）尽可能使用相对 URL 从源映射链接到 Sass 样式表，并在其他情况下使用绝对 [`file:` URL][]。
- `file` 始终使用绝对 `file:` URL 从源映射链接到 Sass 样式表。
- `inline` 直接在源映射中包含 Sass 样式表的文本。
- `none` 完全不生成源映射。

[`file:` URL]: https://en.wikipedia.org/wiki/File_URI_scheme

```shellsession
# 生成类似于 "../sass/style.scss" 的 URL。
$ sass --sourcemap=auto sass/style.scss css/style.css

# 生成类似于 "file:///home/style-wiz/sassy-app/sass/style.scss" 的 URL。
$ sass --sourcemap=file sass/style.scss css/style.css

# 直接在源映射中包含 sass/style.scss 的完整文本。
$ sass --sourcemap=inline sass/style.scss css/style.css

# 不生成源映射。
$ sass --sourcemap=none sass/style.scss css/style.css
```

#### `--stdin`

此标志（缩写为 `-s`）告诉 Sass 从[标准输入][]读取其输入文件。传递该标志时，不能传递输入文件。

```shellsession
$ echo -e 'h1\n  font-size: 40px' | sass --stdin
h1 {
  font-size: 40px;
}
```

`--stdin` 标志不能与[多对多模式][]一起使用。

#### `--default-encoding`

此选项（缩写为 `-E`）控制 Sass 用于加载未[显式指定][]字符编码的源文件的默认[字符编码][]。默认值为操作系统的默认编码。

[字符编码]: https://en.wikipedia.org/wiki/Character_encoding
[显式指定]: /documentation/syntax/parsing#input-encoding

```shellsession
$ sass --default-encoding=Shift-JIS style.scss style.css
```

#### `--unix-newlines`

此标志告诉 Sass 生成行以 U+000A 行进制字符分隔的输出文件，而不是操作系统默认值（在 Windows 上，这是 U+000D 回车后跟 U+000A 行进制）。在默认为 Unix 样式换行符的系统上，它始终为真。

```shellsession
$ sass --unix-newlines style.scss style.css
```

#### `--debug-info`

此标志（缩写为 `-g`）导致 Sass 发出指示每个样式规则在其源样式表中定义位置的伪 `@media` 查询。

{% headsUp %}
  此标志仅用于向后兼容。源映射现在是将 CSS 映射回生成它的 Sass 的推荐方法。
{% endheadsUp %}

```shellsession
$ sass --debug-info style.scss
@media -sass-debug-info{filename{font-family:file\:\/\/\/home\/style-wiz\/sassy-app\/style\.scss}line{font-family:\000031}}
h1 {
  font-size: 40px; }
```

#### `--line-comments`

此标志（也可以作为 `--line-numbers`，缩写为 `-l`）导致 Sass 为每个样式规则发出注释，指示每个样式规则在其源样式表中的定义位置。

```shellsession
$ sass --line-numbers style.scss
/* line 1, style.scss */
h1 {
  font-size: 40px; }
```

### 其他选项

#### `--interactive`

此标志（缩写为 `-i`）告诉 Sass 以交互模式运行，您可以在其中编写 [SassScript 表达式][] 并查看其结果。交互模式还支持[变量][]。

[SassScript 表达式]: /documentation/syntax/structure#expressions
[变量]: /documentation/variables

```shellsession
$ sass --interactive
>> 1px + 1in
97px
>> $map: ("width": 100px, "height": 70px)
("width": 100px, "height": 70px)
>> map-get($map, "width")
100px
```

#### `--check`

此标志（缩写为 `-c`）告诉 Sass 验证其输入文件的语法是否有效而不执行该文件。如果语法有效，它将以[状态][] 0 退出。它不能在[多对多模式][]中使用。

[状态]: https://en.wikipedia.org/wiki/Exit_status

```shellsession
$ sass --check style.scss
```

#### `--precision`

此选项告诉 Sass 在发出十进制数字时使用多少位[精度][]。

[精度]: /documentation/values/numbers#precision

```shellsession
$ echo -e 'h1\n  font-size: (100px / 3)' | sass --precision=20
h1 {
  font-size: 33.333333333333336px; }
```

#### `--cache-location`

此选项告诉 Sass 将其解析文件的缓存存储在何处，以便在未来的调用中运行得更快。默认值为 `.sass-cache`。

```shellsession
$ sass --cache-location=/tmp/sass-cache style.scss style.css
```

#### `--no-cache`

此标志（缩写为 `-C`）告诉 Sass 不缓存解析的文件。

```shellsession
$ sass --no-cache style.scss style.css
```

#### `--trace`

此标志告诉 Sass 在遇到错误时打印完整的 Ruby 堆栈跟踪。Sass 团队用于调试错误。

```shellsession
Traceback (most recent call last):
        25: from /usr/share/gems/sass/bin/sass:13:in `<main>'
        24: from /usr/share/gems/sass/lib/sass/exec/base.rb:18:in `parse!'
        23: from /usr/share/gems/sass/lib/sass/exec/base.rb:50:in `parse'
        22: from /usr/share/gems/sass/lib/sass/exec/sass_scss.rb:63:in `process_result'
        21: from /usr/share/gems/sass/lib/sass/exec/sass_scss.rb:396:in `run'
        20: from /usr/share/gems/sass/lib/sass/engine.rb:290:in `render'
        19: from /usr/share/gems/sass/lib/sass/engine.rb:414:in `_to_tree'
        18: from /usr/share/gems/sass/lib/sass/scss/parser.rb:41:in `parse'
        17: from /usr/share/gems/sass/lib/sass/scss/parser.rb:137:in `stylesheet'
        16: from /usr/share/gems/sass/lib/sass/scss/parser.rb:697:in `block_contents'
        15: from /usr/share/gems/sass/lib/sass/scss/parser.rb:707:in `block_child'
        14: from /usr/share/gems/sass/lib/sass/scss/parser.rb:681:in `ruleset'
        13: from /usr/share/gems/sass/lib/sass/scss/parser.rb:689:in `block'
        12: from /usr/share/gems/sass/lib/sass/scss/parser.rb:697:in `block_contents'
        11: from /usr/share/gems/sass/lib/sass/scss/parser.rb:708:in `block_child'
        10: from /usr/share/gems/sass/lib/sass/scss/parser.rb:743:in `declaration_or_ruleset'
         9: from /usr/share/gems/sass/lib/sass/scss/parser.rb:820:in `try_declaration'
         8: from /usr/share/gems/sass/lib/sass/scss/parser.rb:1281:in `rethrow'
         7: from /usr/share/gems/sass/lib/sass/scss/parser.rb:807:in `block in try_declaration'
         6: from /usr/share/gems/sass/lib/sass/scss/parser.rb:999:in `value!'
         5: from /usr/share/gems/sass/lib/sass/scss/parser.rb:1161:in `sass_script'
         4: from /usr/share/gems/sass/lib/sass/script/parser.rb:68:in `parse'
         3: from /usr/share/gems/sass/lib/sass/script/parser.rb:855:in `assert_expr'
         2: from /usr/share/gems/sass/lib/sass/script/lexer.rb:240:in `expected!'
         1: from /usr/share/gems/sass/lib/sass/scss/parser.rb:1305:in `expected'
test.scss:1: Invalid CSS after "h1 {font-size: ": expected expression (