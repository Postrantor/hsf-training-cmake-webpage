---
tip: translate by baidu@2024-10-28 13:45:20
exercises: 0
keypoints:
  - Functions and macros allow factorization.
  - CMake has an argument parsing function to help with making functions.
objectives:
  - Know how to make a macro or a function in CMake.
questions:
  - How do I write my own CMake commands?
teaching: 10
title: Functions in CMake
---
Let's take a look at making a CMake macro or function. The only difference is in scope; a macro does not make a new scope, while a function does.

> 让我们来看看如何制作 CMake 宏或函数。唯一的区别在于范围；宏不会创建新的作用域，而函数会。

```cmake
function(EXAMPLE_FUNCTION AN_ARGUMENT)
    set(${AN_ARGUMENT}_LOCAL "I'm in the local scope")
    set(${AN_ARGUMENT}_PARENT "I'm in the parent scope" PARENT_SCOPE)
endfunction()

example_function() # Error
example_function(ONE)
example_function(TWO THREE) # Not error

message(STATUS "${ONE_LOCAL}") # What does this print?
message(STATUS "${ONE_PARENT}") # What does this print?
```

We see the basics of functions above. You can specify required positional arguments after the name; all other arguments are set in `ARGN`; `ARGV` holds all arguments, even the listed positional ones. Since you name variables with strings, you can set variables using names. This is enough to recreate any of the CMake commands. But there's one more thing...

> 我们看到了上面函数的基础知识。您可以在名称后指定所需的位置参数；所有其他参数都设置在“ARGN”中 `ARGV` 包含所有参数，即使是列出的位置参数。由于使用字符串命名变量，因此可以使用名称设置变量。这足以重新创建任何 CMake 命令。但还有一件事。。。

### Parsing arguments

You'll have noticed that there are conventions to calling CMake commands; most commands have all-caps keywords that take 0, 1, or an unlimited number of arguments. This handling is standardized in the [`cmake_parse_arguments`](https://cmake.org/cmake/help/latest/command/cmake_parse_arguments.html) command. Here's how it works:

> 您会注意到调用 CMake 命令有一些约定；大多数命令都有带 0、1 或无限数量参数的全大写关键字。这种处理在[`cmake_parse_arguments` 中是标准化的([https://cmake.org/cmake/help/latest/command/cmake_parse_arguments.html](https://cmake.org/cmake/help/latest/command/cmake_parse_arguments.html))命令。其工作原理如下：

{% include hl_code.html lang="cmake" file="code/06-functions/complex.cmake" %}

> {%included hl_code.html lang=“cmake”file=“code/06 functions/complex.cmake”%}

Note: if you use a macro, then a scope is not created and the signature above will not work - remove the `PARSE_ARGV` keyword and the number of required arguments from the beginning, and add "\${ARGN}") to the end.

> 注意：如果您使用宏，则不会创建作用域，上述签名也将不起作用-从开头删除 `PARSE_ARGV` 关键字和所需参数的数量，并在末尾添加“\${ARGN}”）。

The first argument after the `PARSE_ARGV` keyword and number of required arguments is a prefix that will be attached to the results. The next three arguments are lists, one with single keywords (no arguments), one with keywords that take one argument each, and one with keywords that take any number of arguments. The final argument is `${ARGN}` or `${ARGV}`, without quotes (it will be expanded here). If you are in a function and not a macro, you can use `PARSE_ARGV <N>` at the start of the call, where N is the number of positional arguments to expect. This method allows semicolons in the arguments.

> 在“PARSE_ARGV”关键字和所需参数数量之后的第一个参数是将附加到结果的前缀。接下来的三个参数是列表，一个包含单个关键字（无参数），一个带有关键字，每个关键字带一个参数，一个具有关键字，可以带任意数量的参数。最后一个参数是“${ARGN}”或“${ARGV}”，不带引号（将在此处展开）。如果你在函数而不是宏中，你可以在调用开始时使用 `PARSE_ARGV<N>，其中 N 是预期的位置参数的数量。此方法允许在参数中使用分号。

Inside the function, you'll find:

```cmake
-- ARGV=something;SINGLE;ONE_VALUE;value;MULTI_VALUES;some;other;values;ANOTHER_MULTI_VALUES;even;more;values
-- ARGN=SINGLE;ONE_VALUE;value;MULTI_VALUES;some;other;values;ANOTHER_MULTI_VALUES;even;more;values
-- required_arg_1=something
-- COMPLEX_PREFIX_SINGLE=TRUE
-- COMPLEX_PREFIX_ANOTHER=FALSE
-- COMPLEX_PREFIX_ONE_VALUE=value
-- COMPLEX_PREFIX_ALSO_ONE_VALUE=
-- COMPLEX_PREFIX_MULTI_VALUES=some;other;values
-- COMPLEX_PREFIX_ANOTHER_MULTI_VALUES=even;more;values
-- COMPLEX_PREFIX_UNPARSED_ARGUMENTS=
```

The semicolons here are an explicit CMake list; you can use other methods to make this simpler at the cost of more lines of code.

> 这里的分号是一个显式的 CMake 列表；您可以使用其他方法使其更简单，但需要更多的代码行。

> ## More reading
>
> - Based on [Modern CMake basics/functions](https://cliutils.gitlab.io/modern-cmake/chapters/basics/functions.html%5D) {:.checklist}

{% include links.md %}
