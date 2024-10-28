---
tip: translate by baidu@2024-10-28 13:44:29
exercises: 0
keypoints:
  - Build systems describe exactly how to build targets.
  - Build system generators describe general relationships.
  - Modern CMake is simpler and reduces the chance of build problems.
objectives:
  - Learn about build systems and build system generators.
  - Understand why CMake is used.
  - Newer CMake is better.
questions:
  - What is the difference between a build system and a build system generator?
teaching: 10
title: Introduction
---
Building code is hard. You need long commands to build each part of your code; and you need do to this on many parts of your code.

> 建筑规范很难。您需要长命令来构建代码的每个部分；你需要在代码的许多部分都这样做。

So people came up with **Build Systems**; these had ways to set up dependencies (such as file A needs to be built to build file B), and ways to store the commands used to build each file or type of file. These are language independent (mostly), allowing you to setup builds of almost anything; you can use `make` to build LaTeX documents if you wish. Some common build systems include make (the classic pervasive one), ninja (a newer one from Google designed in the age of build system generators), invoke (a Python one), and rake (Ruby make, nice syntax for Ruby users).

> 所以人们想出了**构建系统**；这些有设置依赖关系的方法（例如需要构建文件 A 来构建文件 B），以及存储用于构建每个文件或文件类型的命令的方法。这些是独立于语言的（大部分），允许您设置几乎任何东西的构建；如果你愿意，你可以使用 `make` 来构建 LaTeX 文档。一些常见的构建系统包括 make（经典的普及型）、ninja（谷歌在构建系统生成器时代设计的一种较新的构建系统）、invoke（Python 构建系统）和 rake（Ruby make，Ruby 用户的良好语法）。

However, this is:

- Mostly hand coded: You have to know all the proper commands
- Platform/compiler dependent: You have to build the commands for each compiler.
- Not aware of dependencies: If you require a library, you have to handle the paths, etc.
- Hard to extend; if you want to use an IDE instead, good luck.

Enter **Build System Generators** (hereby labeled BSGs for brevity). These understand the concepts of your programming language build; they usually support common compilers, languages, libraries, and output formats. These usually write a build system (or IDE) file and then let that do the actual build. The most popular BSG is CMake, which stands for Cross-platform Make. But as we've just shown, it is not really in the same category as make. Other BSGs include Autotools (old, inflexible), Bazel (by Google), SCons (older Python system), Meson (young Python system, very opinionated), and a few others. But CMake has unparalleled support by IDEs, libraries, and compilers. It also scales very well, with small projects able to pick it up easily (modern CMake, anyway), and massive projects like the CERN experiments being able to use it for thousands of modules.

> 输入**构建系统生成器**（为简洁起见，此处标记为 BSG）。这些理解你的编程语言构建的概念；它们通常支持常见的编译器、语言、库和输出格式。这些通常会编写一个构建系统（或 IDE）文件，然后让它进行实际的构建。最受欢迎的 BSG 是 CMake，它代表跨平台 Make。但正如我们刚才所展示的，它与 make 并不属于同一类别。其他 BSG 包括 Autotools（旧的、不灵活的）、Bazel（谷歌）、SCons（旧的 Python 系统）、Meson（年轻的 Python 系统，非常固执己见）和其他一些。但是 CMake 拥有 IDE、库和编译器无与伦比的支持。它也可以很好地扩展，小型项目可以很容易地使用它（无论如何都是现代 CMake），而像 CERN 实验这样的大型项目可以将其用于数千个模块。

```{=html}
<script type="text/javascript" src="https://ssl.gstatic.com/trends_nrtr/2674_RC03/embed_loader.js"></script>
```

```{=html}
<script type="text/javascript"> trends.embed.renderExploreWidget("TIMESERIES", {"comparisonItem":[{"keyword":"/m/0cxh7f","geo":"US","time":"2004-01-01 2021-10-18"},{"keyword":"/g/11cmy51gz6","geo":"US","time":"2004-01-01 2021-10-18"},{"keyword":"/g/11bzyq50jp","geo":"US","time":"2004-01-01 2021-10-18"},{"keyword":"/m/0170rr","geo":"US","time":"2004-01-01 2021-10-18"},{"keyword":"/m/04dl04","geo":"US","time":"2004-01-01 2021-10-18"}],"category":0,"property":""}, {"exploreQuery":"date=all&geo=US&q=%2Fm%2F0cxh7f,%2Fg%2F11cmy51gz6,%2Fg%2F11bzyq50jp,%2Fm%2F0170rr,%2Fm%2F04dl04","guestPath":"https://trends.google.com:443/trends/embed/"}); </script>
```

Note that both CMake and Make are custom languages rather than being built in an existing language, like rake and SCons, etc. While it is nice to consolidate languages, the requirement that you have an external language installed and configured was too high for any of these to catch on for general use.

> 请注意，CMake 和 Make 都是自定义语言，而不是用现有语言构建的，如 rake 和 SCons 等。虽然整合语言很好，但安装和配置外部语言的要求太高，任何一种语言都无法普遍使用。

To recap, you should use CMake if:

- You want to avoid hard-coding paths
- You need to build a package on more than one computer
- You want to use CI (continuous integration)
- You need to support different OSs (maybe even just flavors of Unix)
- You want to support multiple compilers
- You want to use an IDE, but maybe not all of the time
- You want to describe how your program is structured logically, not flags and commands
- You want to use a library
- You want to use tools, like Clang-Tidy, to help you code
- You want to use a debugger

## (More) Modern CMake

CMake has really changed dramatically since it was introduced around 2000. And, by the time of 2.8, it was available in lots of Linux Distribution package managers. However, this means there often are really old versions of CMake "available by default" in your environment. Please, please upgrade and design for newer CMake. No one likes writing or debugging build systems. Using a newer version can cut your build system code in less than half, reduce bugs, integrate better with external dependents, and more. Installing CMake can be as little as one line, and doesn't require sudo access. [See more info here](https://cliutils.gitlab.io/modern-cmake/chapters/intro/installing.html).

> 自 2000 年左右推出以来，CMake 确实发生了巨大的变化。而且，到 2.8 版本时，它已经在许多 Linux 发行版软件包管理器中可用。然而，这意味着在您的环境中通常会有“默认可用”的 CMake 旧版本。请升级并设计新的 CMake。没有人喜欢编写或调试构建系统。使用较新版本可以将构建系统代码减少不到一半，减少错误，更好地与外部依赖项集成等等。安装 CMake 只需一行，不需要 sudo 访问权限。[点击此处查看更多信息](https://cliutils.gitlab.io/modern-cmake/chapters/intro/installing.html).

Somehow, this is difficult to understand, so I'll shout it to make it clearer. **Writing Modern CMake reduces your chances of build problems**. The tools CMake provides are _better_ than the ones you will try to write yourself. CMake works with more compilers, in more situations, than you do. So if you try to add a flag, you'll likely get it wrong for some compilers or OSs, but if you can use a tool CMake gives you instead, then that's CMake's business to get right and not yours.

> 不知怎么的，这很难理解，所以我会大声说出来让它更清楚**编写 Modern CMake 可以减少构建问题的可能性**。CMake 提供的工具比你自己编写的工具更好。CMake 可以在更多的情况下与更多的编译器一起工作。因此，如果你试图添加一个标志，对于某些编译器或操作系统来说，你可能会出错，但如果你能使用 CMake 给你的工具，那么这是 CMake 的工作，而不是你的。

It's not a big deal to install a newer CMake. The issues people open for "this is broken" _far_ outnumber the issues people open because you required a CMake newer than they want to run (I think I've only seen one of those, and they came around when they saw the feature they would have to give up).

> 安装新的 CMake 没什么大不了的。人们因“this is breaked”_far_而打开的问题数量超过了人们打开的问题，因为你需要一个比他们想要运行的更新的 CMake（我想我只见过其中一个，当他们看到他们不得不放弃的功能时，他们就改变了主意）。

> ## Example of Modern CMake
>
> **Bad 2.8 style CMake**: Adding a C++11 flag manually. This is compiler specific, is different for CUDA, and locks in a set version, rather than a minimum version.
>
> **If you require CMake 3.1+**, you can set `CXX_STANDARD`, but only on a final target. Or you can manually list `compile_features` for individual C++11 and C++14 features, and, and all targets using yours will get at least that level set on them.
>
> **If you require CMake 3.8+**, you can just use `compile_features` to set a _minimium_ standard level, like `cxx_std_11`, instead of manually listing a bunch of features. This was used for C++17 and later C++20 and C\_\_23, exclusively.

{: .callout}

## Selecting a minimum in 2023:

What minimum CMake should you _run_ locally, and what minimum should you _support_ for people using your code? Since you are reading this, you should be able to get a release in the last few versions of CMake; do that, it will make your development easier. For support, there are two ways to pick minimums: based on features added (which is what a developer cares about), or on common pre-installed CMakes (which is what a user cares about).

> 您应该在本地运行哪个最低 CMake，以及您应该为使用您代码的人提供什么最低支持？既然你正在阅读本文，你应该能够在 CMake 的最后几个版本中获得一个版本；这样做，会让你的开发更容易。对于支持，有两种方法可以选择最小值：基于添加的功能（这是开发人员关心的），或基于常见的预安装 CMake（这是用户关心的）。

Never select a minimum version older than the oldest compiler version you support. CMake should always be at least as new as your compiler.

> 切勿选择比您支持的最旧编译器版本旧的最小版本。CMake 应该始终至少和编译器一样新。

### What minimum to choose - OS support:

- 3.4: The bare minimum. Never set less.
- 3.7: Debian old-stable.
- 3.10: Ubuntu 18.04.
- 3.11: CentOS 8 (use EPEL or AppSteams, though)
- 3.13: Debian stable.
- 3.16: Ubuntu 20.04.
- 3.19: First to support Apple Silicon.
- latest: pip/conda-forge/homebew/chocolaty, etc.

### What minimum to choose - Features:

- 3.8: C++ meta features, CUDA, lots more
- 3.11: `IMPORTED INTERFACE` setting, faster, FetchContent, `COMPILE_LANGUAGE` in IDEs
- 3.12: C++20, `cmake --build build -j N`, `SHELL:`, FindPython
- 3.14/3.15: CLI, FindPython updates
- 3.16: Unity builds / precompiled headers, CUDA meta features
- 3.17/3.18: Lots more CUDA, metaprogramming
- 3.20: C++23, CUDARCHS, IntelLLVM, NVHPC
- 3.21: Different message types, MSVC 2022, C17 & C23, HIP, MSYS
- 3.24: Package finder integration with downloads, `--fresh`
- 3.25: C++26 support, LTO for CUDA

## Other sources

There are some other places to find good information on the web. Here are some of them:

> 在网上还有其他一些地方可以找到好的信息。以下是其中一些：

{% include showlinks.md %}

{% include links.md %}
