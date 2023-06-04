---
title: 第 2 步 [添加一个库]
description: 学习如何使用 CMake 构建库目标
serial: CMake 工程实践指南-基础教程
---

# 第 2 步 [添加一个库]

通过 [第 1 步](./01_a_basic_starting_point.md) 的学习，我们已经了解了如何使用 CMake 创建一个基本项目。在这一步中，我们将学习如何在我们的项目中创建和使用库。我们还将看到如何使我们的库的使用成为可选的。

## 练习1 - 创建一个库

要在 CMake 中添加库，请使用 [add_library()] 命令并指定应由哪些源文件构成库。

我们可以用一个或多个子目录来组织我们的项目，而不是将所有源文件放在一个目录中。在这种情况下，我们将专门为我们的库创建一个子目录。在这里，我们可以添加一个新的 `CMakeLists.txt` 文件和一个或多个源文件。在顶级 `CMakeLists.txt` 文件中，我们将使用 [add_subdirectory()] 命令将子目录添加到构建中。

创建库后，它会通过 [target_include_directories()] 和 [target_link_libraries()] 连接到我们的可执行目标。

### 要完成的目标

添加和使用库。

### 参考资源

- [add_library()]
- [add_subdirectory()]
- [target_include_directories()]
- [target_link_libraries()]
- [PROJECT_SOURCE_DIR]


### 需要编辑的文件
### 入门
### 构建和运行
### 解决方案

## 练习2 - 将库设置可选

### 要完成的目标
### 参考资源
### 需要编辑的文件
### 入门
### 构建和运行
### 解决方案

[add_library()]: https://cmake.org/cmake/help/latest/command/add_library.html#command:add_library
[add_subdirectory()]: https://cmake.org/cmake/help/latest/command/add_subdirectory.html#command:add_subdirectory
[target_include_directories()]: https://cmake.org/cmake/help/latest/command/target_include_directories.html#command:target_include_directories
[target_link_libraries()]: https://cmake.org/cmake/help/latest/command/target_link_libraries.html#command:target_link_libraries
[PROJECT_SOURCE_DIR]: https://cmake.org/cmake/help/latest/variable/PROJECT_SOURCE_DIR.html#variable:PROJECT_SOURCE_DIR