---
tip: translate by baidu@2024-10-28 13:45:15
exercises: 0
keypoints:
  - A FindPackage.cmake file can factor out package discovery for a package you don't own.
  - A PackageConfig.cmake helps others find your package.
objectives:
  - Understand FindPackage.cmake
  - Understand PackageConfig.cmake
questions:
  - How do I search for packages?
teaching: 10
title: Finding Packages
---
You can \[search for packages\]\[`find_package`\] in CMake in [two ways](https://cmake.org/cmake/help/latest/manual/cmake-packages.7.html); both of them, however, use the same interface. Here's what it would look like:

> 您可以通过[两种方式]在 CMake 中搜索包([https://cmake.org/cmake/help/latest/manual/cmake-packages.7.html](https://cmake.org/cmake/help/latest/manual/cmake-packages.7.html))然而，它们都使用相同的界面。以下是它的外观：

```cmake
find_package(MyPackage 1.2)
```

This will look for a file in the \[`CMAKE_MODULE_PATH`\]\[\] that is named `FindMyPackage.cmake`. If it does not find one, it will look for a file named `MyPackageConfig.cmake` in several places, including `MyPackage_DIR` if that variable exists. You can only perform one of these searches with `MODULE` or `CONFIG`, respectively.

> 这将在\[`CMAKE_MODULE_PATH`\]\[\]中查找一个名为 `FindMyPackage.CMAKE` 的文件。如果找不到，它将在几个地方查找名为“MyPackeConfig.cmake”的文件，包括“MyPacke_DIR”（如果该变量存在）。您只能分别使用“MODULE”或“CONFIG”执行其中一个搜索。

You can add `COMPONENTS` in some cases, if the package supports it, and you can also add `QUIET` to hide extra text, or `REQUIRED` to cause a missing package to fail the configure step.

> 在某些情况下，如果软件包支持，您可以添加“COMPONENTS”，您还可以添加“QUIET”以隐藏额外的文本，或添加“REQUIRED”以使丢失的软件包无法完成配置步骤。

> ## Aside: Environment Hints
>
> Hinting the installation of software package that is installed outside of a system paths works can also be done with environment variables. In CMake 3.12+, individual packages locations can be hinted by setting their installation root path in \[`<PackageName>_ROOT`\]\[\].
>
> ```bash
> export HDF5_ROOT=$HOME/software/hdf5-1.12.0
> ```
>
> Similarly, the variable [`CMAKE_PREFIX_PATH`](https://cmake.org/cmake/help/latest/envvar/CMAKE_PREFIX_PATH.html) can be used to hint a list of installation root paths at once:
>
> ```bash
> export CMAKE_PREFIX_PATH=$HOME/software/hdf5-1.12.0:$HOME/software/boost-1.74.0:$CMAKE_PREFIX_PATH
> ```

{: .callout}

## FindPackage

The older method for finding packages is the FindPackage.cmake method (`MODULE`). This is a CMake or user supplied search script that knows how to look for a package. While there are some conventions here, and some tools to help, there are not many hard-and-fast requirements. A package should at least set the variable `Package_FOUND`. There are 100 or so find packages included in CMake, refer to the documentation for each.

> 查找包的旧方法是 FindPackage.cmake 方法（`MODULE`）。这是一个 CMake 或用户提供的搜索脚本，它知道如何查找包。虽然这里有一些约定，也有一些工具可以帮助，但并没有太多硬性要求。一个包至少应该设置变量“package_FOUND”。CMake 中包含大约 100 个查找包，请参阅每个包的文档。

- See the [included FindPackages here](https://cmake.org/cmake/help/latest/manual/cmake-modules.7.html#find-modules).

> -请参阅[此处包含的 FindPackages]([https://cmake.org/cmake/help/latest/manual/cmake-modules.7.html#find](https://cmake.org/cmake/help/latest/manual/cmake-modules.7.html#find)-模块）。

- Many of the packages took a _long_ time to add imported targets
- The old \[`FindPythonLibs`\]\[\] and \[`FindPythonInterp`\] are in the process of being replaced by \[`FindPython`\], but you need a very new version of CMake for the new ones; 3.12 minimum, 3.15 recommended (and 3.18.2+ ideal).

> -旧的\[`FindPythonLibs\]\[\]和\[` FindPythonInterp\]正在被\[`FoundPython\]取代，但你需要一个非常新版本的 CMake 来替换新版本；最低 3.12，建议 3.15（理想 3.18.2+）。

## PackageConfig

The "better" way to do things is to have an installed package provide its own details to CMake; these are "CONFIG" files and come with many packages. These files can be simpler and smarter, since they don't have to search for the package and query the options, but rather can be generated with the correct paths and options for a particular install. ROOT is an example of a package that is now providing a CONFIG file; another one that is just beginning to is Boost; while CMake includes a \[`FindBoost`\], it has to be updated with each new Boost release, whereas `BoostConfig.cmake` can be included with each Boost release (first version in 1.70). One issue with some packages (TBB, for example) is that they may provide _optional_ CONFIG files, and your packager may not have activated them.

> “更好”的做法是让已安装的软件包向 CMake 提供自己的详细信息；这些是“CONFIG”文件，附带了许多软件包。这些文件可以更简单、更智能，因为它们不必搜索软件包和查询选项，而是可以根据特定安装的正确路径和选项生成。ROOT 是一个现在提供 CONFIG 文件的包的示例；另一个刚刚开始的是 Boost；虽然 CMake 包含一个\['FindBoost'\]，但它必须随着每个新的 Boost 版本而更新，而'BoostConfig.CMake'可以包含在每个 Boost 版本中（1.70 中的第一个版本）。某些包（例如 TBB）的一个问题是，它们可能提供_optional_CONFIG 文件，而您的打包者可能没有激活它们。

To be clear: If you are a package author, _never_ supply a `Find<package>.cmake`, but instead always supply a `<package>Config.cmake` with all your builds. If you are depending on another package, try to look for a Config first, and if that is not available, or often not available, then write a find package for it for your use.

> 需要明确的是：如果你是一个包作者，_never_提供一个 `Find<package>.cmake`，但始终在所有构建中提供一个'<package>Config.cmake`。如果您依赖于另一个包，请先尝试查找 Config，如果 Config 不可用或通常不可用，则为其编写一个 find 包供您使用。

{% include links.md %} {% include cmake_links.md %}
