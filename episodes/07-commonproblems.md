---
tip: translate by baidu@2024-10-28 13:45:05
exercises: 0
keypoints:
  - Setting a CMake version too low.
  - Avoid building inplace.
  - How to select a compiler.
  - How to work with spaces in paths.
objectives:
  - Identify some common mistakes
  - Avoid making common mistakes
questions:
  - What could go possibly wrong?
teaching: 5
title: Common Problems and Solutions
---
Now let's take a look at some common problems with CMake code and with builds.

> 现在让我们来看看 CMake 代码和构建的一些常见问题。

## 1: Low minimum CMake version

```cmake
cmake_minimum_required(VERSION 3.0 FATAL_ERROR)
```

Okay, I had to put this one in. But in some cases, just increasing this number fixes problems. 3.0 or less, for example, has a tendency to do the wrong thing when linking on macOS.

> 好吧，我不得不把这个放进去。但在某些情况下，增加这个数字就可以解决问题。例如，3.0 或更低版本在 macOS 上链接时往往会出错。

Solution: Either set a high minimum version or use the version range feature and CMake 3.12 or better. The lowest version you should ever choose is 3.4 even for an ultra-conservative project; several common issues were fixed by that version.

> 解决方案：要么设置一个高的最低版本，要么使用版本范围功能和 CMake 3.12 或更高版本。即使对于一个极端保守的项目，你应该选择的最低版本也是 3.4；该版本修复了几个常见问题。

## 2: Building inplace

CMake should never be used to build in-place; but it's easy to accidentally do so. And once it happens, you have to manually clean the directory before you can do an out-of-source build again. Because of this, while you can run `cmake .` from the build directory after the initial run, it's best to avoid this form just in case you forget and run it from the source directory. Also, you can add the following check to your `CMakeLists.txt`:

> CMake 永远不应该用于就地构建；但很容易意外地这样做。一旦发生这种情况，您必须手动清理目录，然后才能再次进行源代码外构建。因此，虽然你可以运行“cmake”在初始运行后从构建目录中，最好避免使用此表单，以防您忘记并从源目录中运行它。此外，您可以在 `CMakeLists.txt` 中添加以下检查：

```cmake
### Require out-of-source builds
file(TO_CMAKE_PATH "${PROJECT_BINARY_DIR}/CMakeLists.txt" LOC_PATH)
if(EXISTS "${LOC_PATH}")
    message(FATAL_ERROR "You cannot build in a source directory (or any directory with "
                        "CMakeLists.txt file). Please make a build subdirectory. Feel free to "
                        "remove CMakeCache.txt and CMakeFiles.")
endif()
```

One or two generated files cannot be avoided, but if you put this near the top, you can avoid most of the generated files as well as immediately notifying the user (possibly you) that you've made a mistake.

> 一两个生成的文件是无法避免的，但如果你把它放在顶部附近，你可以避免大多数生成的文件，并立即通知用户（可能是你）你犯了错误。

## 3: Picking a compiler

CMake may pick the wrong compiler on systems with multiple compilers. You can use the environment variables \[`CC`\]\[\] and \[`CXX`\]\[\] when you first configure, or CMake variables \[`CMAKE_CXX_COMPILER`\]\[\], etc. - but you need to pick the compiler _on the first run_; you can't just reconfigure to get a new compiler.

> CMake 可能会在具有多个编译器的系统上选择错误的编译器。您可以在首次配置时使用环境变量\[`CC`\]\[\]和\[`CXX`\]\[\]，或者使用 CMake 变量\['CMake_CXX_COMPILER`\]\\]等。-但你需要在第一次运行时选择编译器；你不能只是重新配置以获得新的编译器。

## 4: Spaces in paths

CMake's list and argument system is very crude (it is a macro language); you can use it to your advantage, but it can cause issues. (This is also why there is no "splat" operator in CMake, like `f(*args)` in Python.) If you have multiple items, that's a list (distinct arguments):

> CMake 的列表和参数系统非常粗糙（它是一种宏语言）；你可以利用它来发挥你的优势，但它可能会导致问题。（这也是 CMake 中没有“splat”运算符的原因，就像 Python 中的 `f（*args）` 一样。）如果你有多个项目，那就是一个列表（不同的参数）：

```cmake
set(VAR a b v)
```

The value of `VAR` is a list with three elements, or the string `"a;b;c"` (the two things are exactly the same). So, if you do:

> VAR 的值是一个包含三个元素的列表，或者字符串“a；b；c”（这两个元素完全相同）。所以，如果你这样做：

```cmake
set(MY_DIR "/path/with spaces/")
target_include_directories(target PRIVATE ${MY_DIR})
```

that is identical to:

```cmake
target_include_directories(target PRIVATE /path/with spaces/)
```

which is two separate arguments, which is not at all what you wanted. The solution is to surround the original call with quotes:

> 这是两个独立的论点，这根本不是你想要的。解决方案是用引号括住原始调用：

```cmake
set(MY_DIR "/path/with spaces/")
target_include_directories(target PRIVATE "${MY_DIR}")
```

Now you will correctly set a single include directory with spaces in it.

{% include links.md %} {% include cmake_links.md %}
