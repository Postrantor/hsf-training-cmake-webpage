---
tip: translate by baidu@2024-10-28 13:45:19
exercises: 10
keypoints:
  - ROOT has a CONFIG package you can use to integrate with CMake.
objectives:
  - Use ROOT a couple of different ways
questions:
  - How do I use ROOT?
teaching: 10
title: ROOT
---
[ROOT](https://root.cern/) is a data analysis framework.

Let's try a couple of ROOT examples; one with the classic variable/global configure and one with the newer target method. You will need a ROOT install or a ROOT docker container to run these examples. You can use `rootproject/root:latest` to test this, which is an official Ubuntu based build. Conda-Forge ROOT + CMake would work too, if you like Conda. (ROOT has tags for lots of other base images, too).

> 让我们尝试几个 ROOT 示例；一个使用经典的变量/全局配置，一个使用较新的目标方法。您需要一个 ROOT 安装或 ROOT docker 容器来运行这些示例。你可以使用 `rootproject/root:latest` 来测试这个，这是一个基于 Ubuntu 的官方版本。如果你喜欢 Conda，Conda Forge ROOT+CMake 也可以工作。（ROOT 也有许多其他基础图像的标签）。

For these examples, you should be using a recent version of ROOT - especially for targets, which is still being worked on. The CONFIG files were added in 6.10, and targets received a lot of work in 6.14+. 6.16 has pretty decent targets.

> 对于这些示例，您应该使用最新版本的 ROOT，特别是对于仍在开发中的目标。CONFIG 文件是在 6.10 中添加的，目标在 6.14+ 中完成了大量工作。6.16 的目标相当不错。

## Example 1: UseROOT

Change to the `code/05a-root` directory. Run:

```bash
cmake -S . -B build
cd build
cmake --build .
root -b -q -x ../CheckLoad.C
```

{% include hl_code.html lang="cmake" file="code/05a-root/CMakeLists.txt" %}

> {%included hl_code.html lang=“cmake”file=“code/05a-root/cCMakeLists.txt”%}

## Example 2: Targets

Change to the `code/05b-root` directory. Run the same command above.

{% include hl_code.html lang="cmake" file="code/05b-root/CMakeLists.txt" %}

> {%included hl_code.html lang=“cmake”file=“code/05b root/cCMakeLists.txt”%}

{% include links.md %}
