---
tip: translate by baidu@2024-10-28 13:44:41
exercises: 10
keypoints:
  - The `cmake_minimum_version` setting has deep implications
  - You need a project line.
  - You should prepare one or more targets to do anything interesting.
objectives:
  - Understand the deep implications of `cmake_minimum_version`
  - Know how to set up a project
  - Know how to make at least one target
questions:
  - How little can I get away with in my CMakeLists?
teaching: 10
title: Your first CMakeLists.txt file
---
# Writing a CMakeLists

The following file is fine for the following examples:

{% include hl_code.html file="code/00-intro/simple.c" lang="c" %}

This file can be compiled with C or C++.

## Starting off

This is the simplest possible `CMakeLists.txt`:

```cmake
cmake_minimum_required(VERSION 3.15)

project(MyProject)

add_executable(myexample simple.cpp)
```

Let's look at the three lines:

1. The \[`cmake_minimum_required`\]\[\] command sets the policies so that the build is exactly like it would be on the listed version of CMake - in other words, CMake "dumbs itself down" to the version you request for any features that could produce a different build. This makes CMake almost perfectly backwards compatible.

> 1.\[`cmake_minimum_required`\]\[\]命令设置策略，使构建与列出的 cmake 版本完全相同，换句话说，cmake 将“自我简化”到您请求的版本，以获得可能产生不同构建的任何功能。这使得 CMake 几乎完全向后兼容。

2. You need to be working on a \[`project`\], and it needs at least a name. CMake assumes a `CXX` (that's C++) and `C` mixed project if you don't give any \[`LANGUAGES`\]\[`project`\].

> 2.你需要做一个\[`项目\]，它至少需要一个名称。如果你没有给出任何\[` LANGUAGES\]\[`project\]，CMake会假设一个` CXX `（即C++）和` C` 混合项目。

3. You need at least one library or executable to do anything interesting. The "thing" you make here is called a "target", and the executable/library has the same name, by default, and it has to be unique in the project. You use \[`add_executable`\]\[\] for programs, and \[`add_library`\]\[\] for libraries.

> 3.你需要至少一个库或可执行文件来做任何有趣的事情。您在这里创建的“东西”称为“目标”，默认情况下，可执行文件/库具有相同的名称，并且在项目中必须是唯一的。程序使用\[`addexecutable\]\[\]，库使用\[` add_library\]\[\]。

Those commands have a few extra arguments that you can give:

```cmake
cmake_minimum_required(VERSION 3.15...3.25)

project(MyProject
  VERSION
    1.0
  DESCRIPTION
    "Very nice project"
  LANGUAGES
    CXX
)

add_executable(myexample simple.cpp)
```

1. You can specify a range of versions - this will cause the policies to be set to the highest supported value in that range. As a general rule, set the highest version you've tested with here.

> 1.您可以指定一个版本范围，这将导致策略设置为该范围内支持的最高值。一般来说，在此处设置您测试过的最高版本。

2. Projects can have versions, descriptions, and languages.
3. Whitespace doesn't matter. Be clear/pretty, or use [cmake-format](https://cmake-format.readthedocs.io/en/latest/).

> 3.空白并不重要。清晰/美观，或使用 [cmake-formace](https://cmake-format.readthedocs.io/en/latest/).

::: challenge

```{=html}
<h2>
```

Try it out

```{=html}
</h2>
```

{%- capture tmp -%} Build and run the example code with a `CMakeLists.txt` similar to the one above.

> ｛%-capture tmp-%｝使用与上面类似的 `CMakeLists.txt ` 构建并运行示例代码。

```bash
git clone https://github.com/hsf-training/hsf-training-cmake-webpage.git
cd hsf-training-cmake-webpage/code/00-intro
```

{%- endcapture %} {{ tmp \| markdownify }}

::: solution

```{=html}
<h2>
```

Solution

```{=html}
</h2>
```

{% include hl_code.html lang="cmake" file="code/00-intro/CMakeLists.txt" %}

> {%included hl_code.html lang=“cmake”file=“code/00 intro/cCMakeLists.txt”%}
> :::
> :::

> ## More reading
>
> - Based on [Modern CMake basics](https://cliutils.gitlab.io/modern-cmake/chapters/basics.html) {:.checklist}

{% include cmake_links.md %}
