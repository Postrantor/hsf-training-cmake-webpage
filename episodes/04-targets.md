---
tip: translate by baidu@2024-10-28 13:44:44
exercises: 15
keypoints:
  - Libraries and executables are targets.
  - Targets have lots of useful properties.
  - Targets can be linked to other target.
  - You can control what parts of a target get inherited when linking.
  - You can make INTERFACE targets instead of making variables.
objectives:
  - Know how to set up targets
  - Understand linking and INTERFACE properties
  - Make INTERFACE targets
questions:
  - How do targets work?
teaching: 10
title: Working with Targets
---
# Targets

Now you know how to compile a single file using three lines of CMake. But what happens if you have more than one file with dependencies? You need to be able to tell CMake about the structure of your project, and it will help you build it. To do so, you will need targets.

> 现在，您知道如何使用三行 CMake 编译单个文件。但是，如果您有多个具有依赖关系的文件，会发生什么？你需要能够告诉 CMake 你的项目的结构，这将帮助你构建它。为此，你需要目标。

You've already seen a target:

```cmake
add_executable(myexample simple.cpp)
```

This creates an "executable" target with the name `myexample`. Target names must be unique (and there is a way to set the executable name to something other than the target name if you really want to).

> 这将创建一个名为“myexample”的“可执行”目标。目标名称必须是唯一的（如果你真的想的话，有一种方法可以将可执行文件名称设置为目标名称以外的名称）。

Targets are much like "objects" in other languages; they have properties (member variables) that hold information. The \[`SOURCES`\]\[\] property, for example, will have `simple.cpp` in it.

> 目标很像其他语言中的“对象”；它们具有保存信息的属性（成员变量）。例如，\[`SOURCES`\]\[\]属性将包含 `simple.cpp`。

Another type of target is a library:

```cmake
add_library(mylibrary simplelib.cpp)
```

You can add the keywords `STATIC`, `SHARED`, or `MODULE` if you know what kind of library you want to make; the default is sort-of an "auto" library that is user selectable with `BUILD_SHARED_LIBS`.

> 如果你知道你想创建什么样的库，你可以添加关键字“STATIC”、“SHARED”或“MODULE”；默认值是一种“自动”库，用户可以使用“BUILD_SHARED_LIBS”进行选择。

You can make non-built libraries too. More on that later, once we see what we can do with targets.

> 你也可以创建非构建库。稍后，当我们看到我们可以对目标做些什么时，我们将对此进行更多讨论。

## Linking

Once you have several targets, you can describe the relationship between them with \[`target_link_libraries`\]\[\] and a keyword; one of `PUBLIC`, `PRIVATE`, and `INTERFACE`. Don't forget this keyword when making a library! CMake goes into an old compatibility mode for this target that generally breaks things.

> 一旦你有了几个目标，你可以用\[`target_link_librarys\]\[\]和一个关键字来描述它们之间的关系；“公共”、“私人”和“界面”中的一种。创建库时不要忘记这个关键字！CMake 为此目标进入了一种旧的兼容模式，这通常会破坏一些东西。

> ## Question
>
> You have a library, `my_lib`, made from `my_lib.hpp` and `my_lib.cpp`. It requires at least C++14 to compile. If you then add `my_exe`, and it needs `my_lib`, should that force `my_exe` to compile with C++14 or better?
>
>> ## Answer
>>
>> This depends on the header. If the header contains C++14, this is a PUBLIC requirement - both the library and it's users need it. However, if the header is valid in all versions of C++, and only the implementations inside `my_lib.cpp` require C++14, then this is a `PRIVATE` requirement
>>
>> - users don't need to be forced into C++14 mode.
>>
>> Maybe you do require users have C++14, but your library can compile with any version of C++. This would be an `INTERFACE` requirement. {:.solution}
>>

{:.challenge}

[Example of Public and Private inheritance]{.image}{:height="500px" }

\<!-- Spaces added to -- \> to allow comment to continue graph TD P(Public Library) -- \> \|PUBLIC\| M((Main Library)) R(Private Library) -- \> \|PRIVATE\| M I(Interface Library) -- \> \|INTERFACE\| T P -- \> \|PUBLIC\| T M -- \> T{Target: mylibrary} T -- \> E\[Target: myprogram\] --\>

> \<！--在--\>中添加空格，以允许注释继续图 TD P（公共库）--\>\|Public\|M（主库））R（私有库）--\\>\|Private\|M I（接口库）--\>\|Interface\|T P---\>\ |Public \|T M---\>T{目标：mylibrary}T---\>E\[目标：myprogram\]--\>

Figure 1: Example of PUBLIC, PRIVATE, and INTERFACE. `myprogram` will build the three libraries it sees through `mylibrary`; the private library will not affect it.

> 图 1：公共、私有和界面示例 `myprogram 将构建它通过 mylibrary 看到的三个库；私人图书馆不会影响它。

There are two collections of properties on every target that can be filled with values; the "private" properties control what happens when you build that target, and the "interface" properties tell targets linked to this one what to do when building. The `PUBLIC` keyword fills both property fields at the same time.

> 每个目标上都有两个可以填充值的属性集合；“private”属性控制构建该目标时会发生什么，“interface”属性告诉链接到此目标的目标在构建时要做什么。关键字“PUBLIC”同时填充两个属性字段。

## Example 1: Include directories

When you run \[`target_include_directories(TargetA PRIVATE mydir)`\]\[`target_include_directories`\], then the \[`INCLUDE_DIRECTORIES`\]\[\] property of `TargetA` has `mydir` appended. If you use the keyword `INTERFACE` instead, then \[`INTERFACE_INCLUDE_DIRECTORIES`\]\[\] is appended to, instead. If you use `PUBLIC`, then both properties are appended to at the same time.

> 当您运行\[`target_include_directories（TargetA PRIVATE mydir）`\]\[`target_include-directories`\]时，`TargetA` 的\[`include_directories`\]\\]属性会附加 `mydir`。如果你使用关键字“INTERFACE”，那么\[`INTERFACE_INCLUDE_DIRECTORIES\]\[\]将被附加到。如果使用“PUBLIC”，则两个属性将同时附加。

## Example 2: C++ standard

There is a C++ standard property - \[`CXX_STANDARD`\]\[\]. You can set this property, and like many properties in CMake, it gets it's default value from a \[`CMAKE_CXX_STANDARD`\]\[\] variable if it is set, but there is no INTERFACE version - you cannot force a \[`CXX_STANDARD`\]\[\] via a target. What would you do if you had a C++11 interface target and a C++14 interface target and linked to both?

> 有一个 C++ 标准属性-\[`CXX_standard\]\[\]。您可以设置此属性，与CMake中的许多属性一样，如果设置了它，它将从\[` CMake_CXX_STANDARD `\]\[\]变量中获取默认值，但没有INTERFACE版本-您无法通过目标强制\[` CXX_STANDARD`\]\[\]。如果你有一个 C++11 接口目标和一个 C++14 接口目标，并链接到两者，你会怎么做？

By the way, there _is_ a way to handle this - you can specify the minimum compile features you need to compile a target; the `cxx_std_11` and similar meta-features are perfect for this - your target will compile with at least the highest level specified, unless \[`CXX_STANDARD`\]\[\] is set (and that's a nice, clear error if you set \[`CXX_STANDARD`\]\[\] too low). \[`target_compile_features`\]\[\] can fill \[`COMPILE_FEATURES`\]\[\] and \[`INTERFACE_COMPILE_FEATURES`\]\[\], just like directories in example 1.

> 顺便说一句，有一种方法可以处理这个问题——你可以指定编译目标所需的最小编译特性；“cxx_std_11”和类似的元功能非常适合此情况——除非设置了\[`cxx_STANDARD\]\[\]，否则您的目标将至少使用指定的最高级别进行编译（如果您将\['cxx_STAND\]\[\]设置得太低，这是一个很好、很明显的错误）\[` target_copile_features ` \]\[\]可以填充\[` compile_features ` \]\[\]和\[` INTERFACE_compile_FEAURES` \][\]，就像示例 1 中的目录一样。

::: challenge

```{=html}
<h2>
```

Try it out

```{=html}
</h2>
```

{%- capture tmp -%} Get this repository and go to the example. Try to write a CMakeLists that will correctly build.

> ｛%-capture tmp-%｝获取此存储库并转到示例。尝试编写一个能够正确构建的 CMakeLists。

```bash
git clone https://github.com/hsf-training/hsf-training-cmake-webpage.git
cd hsf-training-cmake-webpage/code/01-simple
```

The files here are:

- simple_lib.cpp: Must be compiled with `MYLIB_PRIVATE` and `MYLIB_PUBLIC` defined.
- simple_example.cpp: Must be compiled with `MYLIB_PUBLIC` defined, but not `MYLIB_PRIVATE`

Use \[`target_compile_definitions(<target> <private or public> <definition(s)>)`\]\[`target_compile_definitions`\] to set the definitions on `simple_lib`. {%- endcapture %} {{ tmp \| markdownify }}

> 使用\[`target_compile-definitions（<target><private或public><definition（s）>）`\]\[`target_compile_definitions`\]在 `simple_lib` 上设置定义。｛%-endcapture%｝｛｛tmp \| markdownify｝｝

::: solution

```{=html}
<h2>
```

Solution

```{=html}
</h2>
```

{% include hl_code.html lang="cmake" file="code/01-simple/CMakeLists.txt" %}

> {%included hl_code.html lang=“cmake”file=“code/01-simple/cCMakeLists.txt”%}
> :::
> :::

### Things you can set on targets

- \[`target_link_libraries`\]\[\]: Other targets; can also pass library names directly
- \[`target_include_directories`\]\[\]: Include directories
- \[`target_compile_features`\]\[\]: The compiler features you need activated, like `cxx_std_11`
- \[`target_compile_definitions`\]\[\]: Definitions
- \[`target_compile_options`\]\[\]: More general compile flags
- \[`target_link_directories`\]\[\]: Don't use, give full paths instead (CMake 3.13+)
- \[`target_link_options`\]\[\]: General link flags (CMake 3.13+)
- \[`target_sources`\]\[\]: Add source files

See more [commands here](https://cmake.org/cmake/help/latest/manual/cmake-commands.7.html).

> 查看更多[命令](https://cmake.org/cmake/help/latest/manual/cmake-commands.7.html).

## Other types of targets

You might be really excited by targets and are already planning out how you can describe your programs in terms of targets. That's great! However, you'll quickly run into two more situations where the target language is useful, but you need some extra flexibility over what we've covered.

> 你可能会对目标感到非常兴奋，并且已经在计划如何用目标来描述你的项目。太棒了！然而，你很快就会遇到另外两种情况，在这些情况下，目标语言是有用的，但你需要在我们所涵盖的范围之外有一些额外的灵活性。

First, you might have a library that conceptually should be a target, but doesn't actually have any built components - a "header-only" library. These are called interface libraries in CMake and you would write:

> 首先，您可能有一个在概念上应该是目标的库，但实际上没有任何构建的组件——一个“仅包含头”的库。这些在 CMake 中被称为接口库，你可以写：

```cmake
add_library(some_header_only_lib INTERFACE)
```

Notice you didn't need to add any source files. Now you can set `INTERFACE` properties on this only (since there is no built component).

> 请注意，您不需要添加任何源文件。现在，您可以仅对此设置“INTERFACE”属性（因为没有内置组件）。

The second situation is if you have a pre-built library that you want to use. This is called an imported library in CMake, and uses the keyword `IMPORTED`. Imported libraries can also be `INTERFACE` libraries, they can be built and modified using the same syntax as other libraries (starting in CMake 3.11), and they can have `::` in their name. (`ALIAS` libraries, which simply rename some other library, are also allowed to have `::`). Most of the time you will get imported libraries from other places, and will not be making your own.

> 第二种情况是，如果你有一个你想使用的预构建库。这在 CMake 中称为导入库，并使用关键字“imported”。导入的库也可以是“INTERFACE”库，它们可以使用与其他库相同的语法（从 CMake 3.11 开始）进行构建和修改，并且它们的名称中可以有“：：”。（“ALIAS”库只需重命名其他库，也允许有“：：”）。大多数时候，你会从其他地方获得导入的库，而不会自己制作。

> ## INTERFACE IMPORTED
>
> What about [`INTERFACE IMPORTED`](https://cmake.org/cmake/help/latest/command/add_library.html#id6)? The difference comes down to two things:
>
> 1. `IMPORTED` targets are not exportable. If you save your targets, you can't save IMPORTED ones - they need to be recreated (or found again).
> 2. `IMPORTED` header include directories will always be marked as `SYSTEM`.
>
> Therefore, an `IMPORTED` target should represent something that is not directly part of your package. {:.callout}

> ## More reading
>
> - Based on [Modern CMake basics](https://cliutils.gitlab.io/modern-cmake/chapters/basics.html)
> - Also see [CMake's docs](https://cmake.org/cmake/help/latest/index.html) {:.checklist}

{% include cmake_links.md %}
