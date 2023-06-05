---
title: 第 3 步 [为库添加使用要求]
description: 学习如何添加库的使用要求。
serial: CMake 工程实践指南-基础教程
---

# 第 3 步 [为库添加使用要求]

## 练习 1 - 为库添加使用要求

[目标参数的使用要求](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#target-usage-requirements)允许更好地控制库或可执行文件的链接和包含内容，同时还可以更好地控制 CMake 内部目标的传递属性。相关 CMake 命令如下：

- [target_compile_definitions()]
- [target_compile_options()]
- [target_include_directories()]
- [target_link_directories()]
- [target_link_options()]
- [target_precompile_headers()]
- [target_sources()]

### 要完成的目标

添加库的使用要求。

### 参考资源

- [CMAKE_CURRENT_SOURCE_DIR]

### 需要编辑的文件

- `MathFunctions/CMakeLists.txt`
- `CMakeLists.txt`

### 入门

在本练习中，我们将从[添加库](./02_adding_a_library.md)重构代码以使用现代 CMake 方法。我们将让我们的库定义自己的使用要求，以便根据需要将它们传递给其他目标。在这种情况下，`MathFunctions` 将自己指定任何需要的包含目录。然后，消费目标 `Tutorial` 只需要链接到 `MathFunctions`，而不用担心任何额外的包含目录。

起始源代码在 `Step3` 目录中提供。在本练习中，完成 `TODO 1` 到 `TODO 3`。

首先，在 `MathFunctions/CMakeLists` 中添加对 [target_include_directories()] 的调用。请记住，[CMAKE_CURRENT_SOURCE_DIR] 是当前正在处理的源目录的路径。

然后，更新（并简化！）顶层 `CMakeLists.txt` 中对 [target_include_directories()] 的调用。

### 构建和运行

运行 [cmake] 可执行文件或 [cmake-gui] 来配置项目，然后使用您选择的构建工具或使用 `cmake --build` 构建它。

``` shell
cmake -S . -B build
cmake --build build
```

接下来，使用新构建的 `Tutorial` 并验证它是否按预期工作。

### 解决方案

让我们更新上一步中的代码，以使用现代 CMake 的使用要求方法。

我们想声明任何链接到 `MathFunctions` 的人都需要包含当前源目录，而 `MathFunctions` 本身不需要。这可以用 `INTERFACE` 使用要求来表达。请记住 `INTERFACE` 意味着消费者需要但生产者不需要的东西。

在 `MathFunctions/CMakeLists.txt` 的最后，使用带有 `INTERFACE` 关键字的 [target_include_directories()]，如下：

??? example "点击展开查看 `TODO 1`"

    ``` cmake title="MathFunctions/CMakeLists.txt"
    target_include_directories(MathFunctions
        INTERFACE
            ${CMAKE_CURRENT_SOURCE_DIR}
    )
    ```

现在我们已经指定了 `MathFunctions` 的使用要求，我们可以安全地从顶级 `CMakeLists.txt` 中删除我们对 `EXTRA_INCLUDES` 变量的使用：

??? example "点击展开查看 `TODO 2`"

    ``` cmake title="CMakeLists.txt"
    if(USE_MYMATH)
        add_subdirectory(MathFunctions)
        list(APPEND EXTRA_LIBS MathFunctions)
    endif()
    ```

??? example "点击展开查看 `TODO 3`"

    ``` cmake title="CMakeLists.txt"
    target_include_directories(Tutorial
        PUBLIC
            "${PROJECT_BINARY_DIR}"
    )
    ```

!!! tip "小提示"

    请注意，使用这种技术，我们的可执行目标为使用我们的库所做的唯一事情就是使用库目标的名称调用 [target_link_libraries()]。在较大的项目中，手动指定库依赖项的经典方法很快就会变得非常复杂。

[target_compile_definitions()]: https://cmake.org/cmake/help/latest/command/target_compile_definitions.html#command:target_compile_definitions
[target_compile_options()]: https://cmake.org/cmake/help/latest/command/target_compile_options.html#command:target_compile_options
[target_include_directories()]: https://cmake.org/cmake/help/latest/command/target_include_directories.html#command:target_include_directories
[target_link_directories()]: https://cmake.org/cmake/help/latest/command/target_link_directories.html#command:target_link_directories
[target_link_options()]: https://cmake.org/cmake/help/latest/command/target_link_options.html#command:target_link_options
[target_precompile_headers()]: https://cmake.org/cmake/help/latest/command/target_precompile_headers.html#command:target_precompile_headers
[target_sources()]: https://cmake.org/cmake/help/latest/command/target_sources.html#command:target_sources
[CMAKE_CURRENT_SOURCE_DIR]: https://cmake.org/cmake/help/latest/variable/CMAKE_CURRENT_SOURCE_DIR.html#variable:CMAKE_CURRENT_SOURCE_DIR
[cmake]: https://cmake.org/cmake/help/latest/manual/cmake.1.html#manual:cmake(1)
[cmake-gui]: https://cmake.org/cmake/help/latest/manual/cmake-gui.1.html#manual:cmake-gui(1)
[ccmake]: https://cmake.org/cmake/help/latest/manual/ccmake.1.html#manual:ccmake(1)