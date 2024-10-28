---
tip: translate by baidu@2024-10-28 13:45:10
exercises: 10
keypoints:
  - There are several methods for debugging your CMake code.
  - CMake can help you debug and profile your source code.
objectives:
  - Know how to find problems in CMake
  - Know how to set up builds for debugging
questions:
  - How do I debug everything?
teaching: 10
title: Debugging
---
Debugging is easy with CMake. We'll cover two forms of debugging: debugging your CMake code, and debugging your C++ code.

> 使用 CMake 调试很容易。我们将介绍两种形式的调试：调试 CMake 代码和调试 C++ 代码。

# CMake debugging

First, let's look at ways to debug a CMakeLists or other CMake file.

### Printing variables

The time honored method of print statements looks like this in CMake:

```cmake
message(STATUS "MY_VARIABLE=${MY_VARIABLE}")
```

However, a built in module makes this even easier:

```cmake
include(CMakePrintHelpers)
cmake_print_variables(MY_VARIABLE)
```

If you want to print out a property, this is much, much nicer! Instead of getting the properties one by one of of each target (or other item with properties, such as `SOURCES`, `DIRECTORIES`, `TESTS`, or `CACHE_ENTRIES` - global properties seem to be missing for some reason), you can simply list them and get them printed directly:

> 如果你想打印出房产，这会好得多！您可以简单地列出它们并直接打印出来，而不是逐一获取每个目标的属性（或其他具有属性的项，如“SOURCES”、“DIRECTORIES”、“TESTS”或“CACHE_ENTRIES”-由于某种原因，全局属性似乎缺失）：

```cmake
cmake_print_properties(
    TARGETS my_target
    PROPERTIES POSITION_INDEPENDENT_CODE
)
```

> ## Warning
>
> You can't actually access \[`SOURCES`\]\[\], since it conflictes with the `SOURCES` keyword in the function. {:.callout}

### Tracing a run

Have you wanted to watch exactly what happens in your CMake file, and when? The `--trace-source="filename"` feature is fantastic. Every line run in the file that you give will be echoed to the screen when it is run, letting you follow exactly what is happening. There are related options as well, but they tend to bury you in output.

> 你想看看 CMake 文件中到底发生了什么，什么时候发生的吗？`--trace-source=“filename”` 功能非常棒。您给出的文件中的每一行在运行时都会回显到屏幕上，让您可以准确地了解正在发生的事情。也有相关的选项，但它们往往会让你沉浸在输出中。

> ## Watching a build
>
> Let's try this out. Let's go to the [`code/04-debug`]() folder and configure with trace mode on:
>
> ```bash
> cmake -S . -B build --trace-source=CMakeLists.txt
> ```
>
> Try adding `--trace-expand` too. What is the difference? How about replacing `--trace-source=CMakeLists.txt` with `--trace`?

{:.challenge}

### Find call information

CMake scripts can search for dependent libraries, executables and more. More details on this will be shown in the next section.

> CMake 脚本可以搜索依赖库、可执行文件等。更多细节将在下一节中显示。

For now, let's watch where CMake searches for `find_...` locations in our current example! You can print extra find call information during the cmake run to standard error by adding `--debug-find` (CMake 3.17+).

> 现在，让我们看看 CMake 在哪里搜索“find_…”我们当前示例中的位置！通过添加“--debug find”（cmake 3.17+），您可以在 cmake 运行期间将额外的 find 调用信息打印为标准错误。

Alternatively, [CMAKE_FIND_DEBUG_MODE](https://cmake.org/cmake/help/latest/variable/CMAKE_FIND_DEBUG_MODE.html) can be set around sections of your `CMakeLists.txt` to limit debug printing to a specific region.

> 或者，[CMAKE_FIND_DEBUG_MODE](https://cmake.org/cmake/help/latest/variable/CMAKE_FIND_DEBUG_MODE.html) 可以围绕“CMakeLists.txt”的部分进行设置，以将调试打印限制在特定区域。

# C++ debugging

To run a C++ debugger, you need to set several flags in your build. CMake does this for you with "build types". You can run CMake with `CMAKE_BUILD_TYPE=Debug` for full debugging, or `RelWithDebInfo` for a release build with some extra debug info. You can also use `Release` for an optimized release build, or `MinSizeRel` for a minimum size release (which I've never used).

> 要运行 C++ 调试器，您需要在构建中设置几个标志。CMake 通过“构建类型”为您完成此操作。您可以使用“CMake_BUILD_TYPE=Debug”运行 CMake 进行完整调试，或使用“RelWithDebInfo”运行带有一些额外调试信息的发布版本。您还可以使用“Release”进行优化的版本构建，或使用“MinSizeRel”进行最小大小的版本发布（我从未使用过）。

::: challenge

```{=html}
<h2>
```

Debug example

```{=html}
</h2>
```

```{=html}
<details>
```

```{=html}
<summary>
```

C++ code (click to expand)

```{=html}
</summary>
```

{% include hl_code.html lang="cmake" file="code/04-debug/simple_lib.c" %}

```{=html}
</details>
```

{% capture tmp %} Let's try it. Go to `code/04-debug`, and build in debug mode. Our program has a bug. Let's try it out in a debugger.

> ｛%capture tmp%｝让我们试试。转到“code/04-debug”，在调试模式下构建。我们的程序有个 bug。让我们在调试器中尝试一下。

```bash
cmake -S . -B build-debug -DCMAKE_BUILD_TYPE=Debug
cmake --build build-debug
gdb build-debug/simple_example
```

Now, since we think there's a problem in `my_sin`, let's set a breakpoint in `my_sin`. Note that I'm providing the gdb commands on the left, and lldb commands on the right.

> 现在，由于我们认为 `my_sin` 中存在问题，让我们在 `my_sin ` 中设置一个断点。请注意，我在左侧提供 gdb 命令，在右侧提供 lldb 命令。

```
# GDB                # LLDB
break my_sin         breakpoint set --name my_sin
r                    r
```

Now, let's watch what happens to the sign variable. Set a watchpoint:

```
# GDB                # LLDB
watch sign           watchpoint set variable sign
c                    c
```

Keep running continue (`c`). Do you find the problem in the code? {% endcapture %} {{ tmp \| markdownify }}

> 继续运行（`c`）。你在代码中找到问题了吗？｛%endcapture%｝｛｛tmp \| markdownify｝｝
> :::

> ## Aside: Linking to math
>
> You may find that the example provided does not work unless it's linked with the math library "m", which looks like `-lm` when linking with gcc (llvm does not seem to need to link to it). Let's look for the "m" library:
>
> ```cmake
> # Does -lm work? (notice this is find_library, not find_package)
> find_library(MATH_LIBRARY m)
> ```
>
> If it is found, this saves the location of the m library in a variable that we gave it the name of, in our case, `MATH_LIBRARY`. We can add the path (not a target) using the same \[`target_link_libraries`\]\[\] command. It is very unfortunate that this command happens to accept both targets and raw paths and linker flags, but it's a historical leftover.
>
> ```cmake
> # If there is a -lm, let's use it
> if(MATH_LIBRARY)
>     target_link_libraries(simple_lib PUBLIC ${MATH_LIBRARY})
> endif()
> ```

{: .callout}

Note that CMake defaults to an "empty" build type, which is neither optimized nor debug. You can [fix this manually](https://cliutils.gitlab.io/modern-cmake/chapters/features.html), or always specify a build type.

> 请注意，CMake 默认为“空”构建类型，既没有优化也没有调试。您可以[手动修复此问题](https://cliutils.gitlab.io/modern-cmake/chapters/features.html)，或始终指定生成类型。

Adopting a convention from Linux, all build types append compiler flags from the environment variables \[`CFLAGS`\]\[\], \[`CXXFLAGS`\]\[\], \[`CUDAFLAGS`\]\[\], and \[`LDFLAGS`\]\[\] ([full list](https://cmake.org/cmake/help/latest/manual/cmake-env-variables.7.html#id4)). This feature is often used by package management software, in conjunction with the already mentioned \[`CC`\]\[\], \[`CXX`\]\[\], \[`CUDACXX`\]\[\], and \[`CUDAHOSTCXX`\]\[\] environment variables. Otherwise, you can set the release and debug flags separately.

> 采用 Linux 的约定，所有构建类型都会从环境变量\['CFLAGS\]\[\]、\['CXXFLAGS\][\]、\['CUDAFLAGS\]\[\]和\['LDFLAGS\]中附加编译器标志（[完整列表](https://cmake.org/cmake/help/latest/manual/cmake-env-variables.7.html#id4)). 此功能通常由包管理软件使用，并与前面提到的\[`CC\]\[\]、\[` CXX\]\[]、\['CUDACXX\]\\\]和\[`CUDAHOSTCXX\][\]环境变量结合使用。否则，您可以分别设置发布和调试标志。

# Common needs

There are several common utilities that CMake can integrate with to help you with your builds. Here are just a few:

> CMake 可以集成几个常见的实用程序来帮助您进行构建。以下是一些：

- \[`CMAKE_CXX_COMPILER_LAUNCHER`\]\[\] can set up a compiler launcher, like `ccache`, to speed up your builds.

> -\[`CMAKE_CXX_COMPILER_LAUNCHER `\]\[\]可以设置编译器启动器，如 `ccache `，以加快构建速度。

- \[`CMAKE_CXX_CLANG_TIDY`\]\[\] can run clang-tidy to help you clean up your code.
- \[`CMAKE_CXX_CPPCHECK`\]\[\] for cppcheck.
- \[`CMAKE_CXX_CPPLINT`\]\[\] for cpplint.
- \[`CMAKE_CXX_INCLUDE_WHAT_YOU_USE`\]\[\] for iwyu.

You can set these when building if you want.

{% include links.md %} {% include cmake_links.md %}
