---
tip: translate by baidu@2024-10-28 13:44:58
exercises: 10
keypoints:
  - Projects should be well organised.
  - Subproject CMakeLists are used for tests and more.
objectives:
  - Know some best practices for project structure
questions:
  - What should my project look like?
teaching: 10
title: Project Structure
---
For this section, we will be looking at the project in [`code/03-structure`](%7B%7B%20site.baseurl%20%7D%7D/code/03-structure).

> 在本节中，我们将查看[“代码/03 结构”]中的项目（%7B%7B%20site.baseurl%20%7D%7D/code/03 结构）。

```
code/03-structure/
├── CMakeLists.txt
├── README.md
├── apps
│   ├── CMakeLists.txt
│   └── app.cpp
├── cmake
│   └── FindSomeLib.cmake
├── docs
│   ├── CMakeLists.txt
│   └── mainpage.md
├── include
│   └── modern
│       └── lib.hpp
├── src
│   ├── CMakeLists.txt
│   └── lib.cpp
└── tests
    ├── CMakeLists.txt
    └── testlib.cpp
```

First, take a look at the main [`CMakeLists.txt`](%7B%7B%20site.baseurl%20%7D%7D/code/03-structure/CMakeLists.txt) file. This is an example of a nice project file in CMake 3.14, so enjoy it for a minute. Now let's look at specifics!

> 首先，查看主[`CMakeLists.txt`]（%7B%7B%20site.baseurl%20%7D%7D/code/03-structure/cCMakeLists.txt）文件。这是 CMake 3.14 中一个很好的项目文件的示例，所以请享受一下。现在让我们来看看具体细节！

```{=html}
<details>
```

```{=html}
<summary>
```

Click to see CMakeLists.txt

```{=html}
</summary>
```

{% include hl_code.html lang="cmake" file="code/03-structure/CMakeLists.txt" %}

> {%included hl_code.html lang=“cmake”file=“code/03-structure/CCMakeLists.txt”%}

```{=html}
</details>
```

### Protect project code

The parts of the project that only make sense if we are building this as the main project are protected; this allows the project to be included in a larger master project with `add_subdirectory`.

> 只有当我们将其作为主要项目进行建设时，项目中有意义的部分才会受到保护；这允许项目包含在一个更大的主项目中，并使用 `addsubdirectory`。

### Testing handled in the main CMakeLists

We have to do a little setup for testing in the main CMakeLists, because you can't run \[`enable_testing`\]\[\] from a subdirectory (and thereby \[`include(CTest)`\]\[`CTest`\]). Also, notice that `BUILD_TESTING` does not turn on testing unless this is the main project.

> 我们必须在主 CMakeLists 中进行一些测试设置，因为您无法从子目录运行\[`enable_testing`\]\[\]（因此也无法运行\[` include（CTest）`\]\[` CTest`\]）。此外，请注意，除非这是主项目，否则“BUILD_TESTING”不会打开测试。

### Finding packages

We find packages in our main CMakeLists, then use them in subdirectories. We could have also put them in a file that was included, such as `cmake/find_pakages.cmake`. If your CMake is new enough, you can even add a subdirectory with the find packages commands, but you have to set `IMPORTED_GLOBAL` on the targets you want to make available if you do that. For small to mid-size projects, the first option is most common, and large projects use the second option (currently).

> 我们在主 CMakeList 中找到包，然后在子目录中使用它们。我们还可以将它们放在包含的文件中，例如 `cmake/find_pakages.cmake`。如果你的 CMake 足够新，你甚至可以使用 find packages 命令添加一个子目录，但如果你这样做，你必须在你想要提供的目标上设置 `IMPORTED_GLOBAL`。对于中小型项目，第一种选择最为常见，而大型项目则使用第二种选择（目前）。

All the find packages here provide imported targets. If you do not have an imported target, **make one**! Never use the raw variables past the lines immediately following the `find_package` command. There are several easy mistakes to make if you do not make imported targets, including forgetting to add `SYSTEM`, and the search order is better (especially before CMake 3.12).

> 这里的所有查找包都提供了导入的目标。如果您没有导入的目标，**创建一个**！永远不要使用 `find_package` 命令后面的行之后的原始变量。如果不创建导入目标，则容易犯几个错误，包括忘记添加“SYSTEM”，搜索顺序更好（特别是在 CMake 3.12 之前）。

In this project, I use the new \[`FetchContent`\]\[\] (3.11/3.14) to download several dependencies; although normally I prefer git submodules in `/extern`.

> 在这个项目中，我使用新的\[`FetchContent\]\[\]（3.11/3.14）下载了几个依赖项；虽然通常我更喜欢`/externan` 中的 git 子模块。

### Source

Now follow the \[`add_subdirectory`\]\[\] command to see the src folder, where a library is created.

> 现在按照\[`add_subdirectory`\]\[\]命令查看创建库的 src 文件夹。

```{=html}
<details>
```

```{=html}
<summary>
```

Click to see src/CMakeLists.txt

```{=html}
</summary>
```

{% include hl_code.html lang="cmake" file="code/03-structure/src/CMakeLists.txt" %}

> {%included hl_code.html lang=“cmake”file=“code/03-structure/src/cCMakeLists.txt”%}

```{=html}
</details>
```

The headers are listed along with the sources in the \[`add_library`\]\[\] command. This would have been another way to do it in CMake 3.11+:

> 标头与源代码一起列在\[`add_library\]\[\]命令中。这将是 CMake 3.11+ 中的另一种方法：

```cmake
add_library(modern_library)
target_sources(modern_library
  PRIVATE
    lib.cpp
  PUBLIC
    ${HEADER_LIST}
)
```

Notice that we have to use \[`target_include_directories`\]\[\]; just adding a header to the sources does not tell CMake what the correct include directory for it should be.

> 请注意，我们必须使用\[`target_include_directories\]\[\]；仅仅在源代码中添加一个标头并不能告诉 CMake 它应该使用哪个正确的 include 目录。

We also set up the \[`target_link_libraries`\]\[\] with the appropriate targets.

> 我们还使用适当的目标设置了\[`target_link_librarys\]\[\]。

### App

Now take a look at `apps/CMakeLists.txt`. This one is pretty simple, since all the leg work for using our library was done on the library target, as it should be.

> 现在看看 `apps/CMakeLists.txt`。这个很简单，因为使用我们的库的所有工作都是在库目标上完成的，这是应该的。

```{=html}
<details>
```

```{=html}
<summary>
```

Click to see apps/CMakeLists.txt

```{=html}
</summary>
```

{% include hl_code.html lang="cmake" file="code/03-structure/apps/CMakeLists.txt" %}

> {%included hl_code.html lang=“cmake”file=“code/03 structure/apps/cCMakeLists.txt”%}

```{=html}
</details>
```

### Docs and Tests

Feel free to look at `docs` and `tests` for their `CMakeLists.txt`.

```{=html}
<details>
```

```{=html}
<summary>
```

Click to see docs/CMakeLists.txt

```{=html}
</summary>
```

{% include hl_code.html lang="cmake" file="code/03-structure/docs/CMakeLists.txt" %}

> {%included hl_code.html lang=“cmake”file=“code/03-structure/docs/cCMakeLists.txt”%}

```{=html}
</details>
```

```{=html}
<details>
```

```{=html}
<summary>
```

Click to see tests/CMakeLists.txt

```{=html}
</summary>
```

{% include hl_code.html lang="cmake" file="code/03-structure/tests/CMakeLists.txt" %}

> ｛%included hl_code.html lang=“cmake”file=“code/03 结构/测试/CMakeLists.txt”%｝

```{=html}
</details>
```

> ## More reading
>
> - Based on [Modern CMake basics/structure](https://cliutils.gitlab.io/modern-cmake/chapters/basics/structure.html) {:.checklist}

{% include links.md %} {% include cmake_links.md %}
