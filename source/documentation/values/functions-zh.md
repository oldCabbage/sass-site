---
title: 函数值
---

{% render 'doc_snippets/call-impl-status' %}

[Functions][] 也可以作为值！你不能直接将一个函数写成一个值，但你可以将函数的名称传递给 [`meta.get-function()` function][] 来获取它的值。一旦你有了一个函数值，你可以将其传递给 [`meta.call()` function][] 来调用它。这对于编写 *高阶函数* 非常有用，这些函数会调用其他函数。

[Functions]: /documentation/at-rules/function
[`meta.get-function()` function]: /documentation/modules/meta#get-function
[`meta.call()` function]: /documentation/modules/meta#call

{% render 'code_snippets/example-first-class-function' %}