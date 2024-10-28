---
tip: translate by baidu@2024-10-28 13:44:52
exercises: 10
keypoints:
  - Local variables work in this directory or below.
  - Cached variables are stored between runs.
  - You can access environment variables, properties, and more.
  - You can glob to collect files from disk, but it might not always be a good idea.
objectives:
  - Learn about local variables.
  - Understand that cached variables persist across runs.
  - Know how to glob, and why you might not do it.
questions:
  - How do variables work?
teaching: 10
title: Variables explained
---
# Variables

For this exercise, we will just directly run a CMake Script, instead of running `CMakeLists.txt`. The command to do so is:

> 对于这个练习，我们将直接运行 CMake 脚本，而不是运行 `CMakeLists.txt`。这样做的命令是：

```bash
# Assuming you have a file called example.cmake:
cmake -P example.cmake
```

This way, we don't have so many little builds sitting around.

## Local variables

Let's start with a local variable.

```cmake
# local.cmake
set(MY_VARIABLE "I am a variable")
message(STATUS "${MY_VARIABLE}")
```

Here we see the \[`set`\]\[\] command, which sets a variable, and the \[`message`\] command, which prints out a string. We are printing a `STATUS` \[`message`\]\[\] - there are other types (many other types in CMake 3.15+).

> 这里我们看到了\[`set`\]\[\]命令，它设置了一个变量，以及\[`message`\]命令打印出一个字符串。我们正在打印一个 `STATUS` \[`message \]\[\]-还有其他类型（CMake 3.15+ 中的许多其他类型）。

> ## More about variables
>
> Try the following:
>
> - Remove the quotes in set. What happens?
> - Remove the quotes in message. What happens? Why?
> - Try setting a cached variable using `-DMY_VARIABLE=something` _before_ the `-P`. Which variable is shown?

{:.challenge}

## Cached variables

Now, let's look at cached variables; a key ingredient in all CMake builds. In a build, cached variables are set in the command line or in a graphical tool (such as `ccmake`, `cmake-gui`), and then written to a file called `CMakeCache.txt`. When you rerun, the cache is read in before starting, so that CMake "remembers" what you ran it with. For our example, we will use CMake in script mode, and that will not write out a cache, which makes it easier to play with. Feel free to look back at the example you built in the last lesson and investigate the `CMakeCache.txt` file in your build directory there. Things like the compiler location, as discovered or set on the first run, are cached.

> 现在，让我们看看缓存变量；这是所有 CMake 构建中的关键要素。在构建中，缓存的变量在命令行或图形工具（如“ccmake”、“cmake-gui”）中设置，然后写入一个名为“CMakeCache.txt”的文件。当你重新运行时，缓存会在启动前被读入，这样 CMake 会“记住”你运行它的内容。对于我们的例子，我们将在脚本模式下使用 CMake，这不会写出缓存，这使得它更容易使用。请随时回顾您在上一课中构建的示例，并研究构建目录中的 `CMakeCache.txt ` 文件。在第一次运行时发现或设置的编译器位置等信息会被缓存。

Here's what a cached variable looks like:

```cmake
# cache.cmake
set(MY_CACHE_VAR "I am a cached variable" CACHE STRING "Description")
message(STATUS "${MY_CACHE_VAR}")
```

We have to include the variable type here, which we didn't have to do before (but we could have) - it helps graphical CMake tools show the correct options. The main difference is the `CACHE` keyword and the description. If you were to run `cmake -L` or `cmake -LH`, you would see all the cached variables and descriptions.

> 我们必须在这里包含变量类型，这是我们以前不必做的（但我们可以做）——它有助于图形 CMake 工具显示正确的选项。主要区别在于“CACHE”关键字和描述。如果你运行 `cmake-L` 或 `cmake-L-L`，你会看到所有缓存的变量和描述。

The normal set command _only_ sets the cached variable if it is not already set - this allows you to override cached variables with `-D`. Try:

> 普通的 set 命令_only_会设置缓存变量（如果尚未设置），这允许您使用 `-D ` 覆盖缓存变量。尝试：

```bash
cmake -DMY_CACHE_VAR="command line" -P cache.cmake
```

You can use `FORCE` to set a cached variable even if it already set; this should not be very common. Since cached variables are global, sometimes they get used as a makeshift global variable - the keyword `INTERNAL` is identical to `STRING FORCE`, and hides the variable from listings/GUIs.

> 您可以使用 `FORCE` 来设置缓存变量，即使它已经设置；这应该不太常见。由于缓存的变量是全局的，有时它们会被用作临时全局变量——关键字“INTERNAL”与“STRING FORCE”相同，并在列表/GUI 中隐藏该变量。

Since bool cached variables are so common for builds, there is a shortcut syntax for making one using \[`option`\]\[\]:

> 由于 bool 缓存变量在构建中非常常见，因此有一种使用\[`option`\]\[\]创建变量的快捷语法：

```cmake
option(MY_OPTION "On or off" OFF)
```

## Other variables

You can get environment variables with `$ENV{name}`. You can check to see if an environment variable is defined with `if(DEFINED ENV{name})` (notice the missing `$`).

> 您可以使用 `$ENV{name}` 获取环境变量。您可以检查环境变量是否使用“if（defined ENV{name}）”定义（注意缺少“$”）。

Properties are a form of variable that is attached to a target; you can use \[`get_property`\]\[\] and \[`set_property`\]\[\], or \[`get_target_properties`\]\[\] and \[`set_target_properties`\]\[\] (stylistic preference) to access and set these. You can [see a list of all properties](https://cmake.org/cmake/help/latest/manual/cmake-properties.7.html) by CMake version; there is no way to get this programmatically.

> 属性是一种附加到目标的变量形式；您可以使用\[`get_properties \]\[\]和\[` set_property\]\[]，或\[`get_target_properties \]\[\]和\[` set_target_proerties \]\[\]（样式首选项）来访问和设置这些属性。您可以[查看所有属性的列表](https://cmake.org/cmake/help/latest/manual/cmake-properties.7.html)按 CMake 版本；无法通过编程方式获取此信息。

> ## Handy tip:
>
> Use \[`include(CMakePrintHelpers)`\]\[`CMakePrintHelpers`\] to add the useful commands `cmake_print_properties` and `cmake_print_variables` to save yourself some typing when debugging variables and properties. {:.callout}

## Target properties and variables

You have seen targets; they have properties attached that control their behavior. Many of these properties, such as \[`CXX_EXTENSIONS`\]\[\], have a matching variable that starts with `CMAKE_`, such as \[`CMAKE_CXX_EXTENSIONS`\]\[\], that will be used to initialize them. So you can using set property on each target by setting a variable before making the targets.

> 你已经看到了目标；它们具有控制其行为的属性。其中许多属性，如\[`CXX_EXTENSIONS`\]\[\]，都有一个以 `CMAKE_` 开头的匹配变量，如\[`CMAKE_CXX_EXTENSIONS`\][\]，将用于初始化它们。因此，您可以在创建目标之前通过设置变量来对每个目标使用 set 属性。

## Globbing

There are several commands that help with \[`string`\]\[\]s, \[`file`\]\[\]s, \[`lists`\]\[\], and the like. Let's take a quick look at one of the most interesting: glob.

> 有几个命令可以帮助处理\[`string \]\[\]s、\[` file \]\[]s、\[lists \]\[\]等。让我们快速看看最有趣的一个：glob。

```cmake
file(GLOB OUTPUT_VAR *.cxx)
```

This will make a list of all files that match the pattern and put it into `OUTPUT_VAR`. You can also use `GLOB_RECURSE`, which will recurse subdirectories. There are several useful options, which you can look at [in the documentation](https://cmake.org/cmake/help/latest/command/file.html?highlight=glob#filesystem), but one is particularly important: `CONFIGURE_DEPENDS` (CMake 3.12+).

> 这将列出与模式匹配的所有文件，并将其放入“OUTPUT_VAR”中。您还可以使用 `GLOB_RECURSE`，它将递归子目录。有几个有用的选项，您可以在[文档中]查看([https://cmake.org/cmake/help/latest/command/file.html?highlight=glob#filesystem](https://cmake.org/cmake/help/latest/command/file.html?highlight=glob#filesystem))，但有一个特别重要：`CONFIGURE_DEPENDS`（CMake 3.12+）。

When you rerun the _build_ step (not the configure step), then unless you set `CONFIGURE_DEPENDS`, your build tool will not check to see if you have added any new files that now pass the glob. This is the reason poorly written CMake projects often have issues when you are trying to add files; some people are in the habit of rerunning `cmake` before every build because of this. You shouldn't ever have to manually reconfigure; the build tool will rerun CMake as needed with this one exception. If you add `CONFIGURE_DEPENDS`, then _most_ build tools will actually start checking glob too. The classic rule of CMake was "never glob"; the new rule is "never glob, but if you have to, add `CONFIGURE_DEPENDS`".

> 当您重新运行_build_步骤（而不是配置步骤）时，除非您设置了 `configure_DEPENDS`，否则您的构建工具将不会检查您是否添加了任何现在通过 glob 的新文件。这就是为什么编写糟糕的 CMake 项目在尝试添加文件时经常出现问题的原因；因此，有些人习惯于在每次构建之前重新运行 cmake。你永远不应该手动重新配置；构建工具将根据需要重新运行 CMake，但有一个例外。如果您添加“CONFIGURE_DEPENDS”，那么_most_build 工具实际上也会开始检查 glob。CMake 的经典规则是“永不全球化”；新规则是“永远不要 glob，但如果必须添加 `CONFIGURE_DEPENDS`”。

> ## More reading
>
> - Based on [Modern CMake basics/variables](https://cliutils.gitlab.io/modern-cmake/chapters/basics/variables.html)
> - Also see [CMake's docs](https://cmake.org/cmake/help/latest/index.html) {:.checklist}

{% include cmake_links.md %}
