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

    请注意，使用这种技术，我们的可执行目标要使用我们的库，唯一要做的就是调用 [target_link_libraries()]，并输入目标库的名称。在大型项目中，手动指定库依赖关系的传统方法很快就会变得非常复杂。

## 练习 2 - 利用接口库制定 C++ 标准

既然我们已经将代码转换为更现代的方法，那么让我们来演示一下为多个目标设置属性的现代技术。

让我们重构现有代码，使用 INTERFACE 库。我们将在下一步中使用该库来演示[生成器表达式]的常用用法。

### 要完成的目标
添加 INTERFACE 库目标，指定所需的 C++ 标准。

### 参考资源
- [add_library()]
- [target_compile_features()]
- [target_link_libraries()]

### 需要编辑的文件
- `CMakeLists.txt`
- `MathFunctions/CMakeLists.txt`

### 入门
在本练习中，我们将重构代码，使用 INTERFACE 库来指定 C++ 标准。

从[第 3 步练习 1](./03_adding_usage_requirements_for_a_library.md#1-)结束时留下的内容开始本练习。您必须完成 `TODO 4` 到 `TODO 7`。

首先编辑顶层 `CMakeLists.txt` 文件。构建名为 `tutorial_compiler_flags` 的 `INTERFACE` 库目标，并指定 `cxx_std_11` 为目标编译器特征。

修改 `CMakeLists.txt` 和 `MathFunctions/CMakeLists.txt`，使所有目标都有 [target_link_libraries()] 调用 `tutorial_compiler_flags`。

### 构建和运行
由于我们已经在[练习 1](./03_adding_usage_requirements_for_a_library.md#1-) 中配置了构建目录，因此只需调用以下命令重建代码即可：

``` shell
cd build
cmake --build .
```

接下来，使用新构建的 `Tutorial`，并验证其是否按预期运行。

### 解决方案
让我们更新上一步的代码，使用接口库来设置 `C++` 要求。

首先，我们需要删除变量 [CMAKE_CXX_STANDARD] 和 [CMAKE_CXX_STANDARD_REQUIRED] 上的两个 [set()] 调用。需要删除的具体行列如下：

``` cmake title="CMakeLists.txt"
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)
```

接下来，我们需要创建一个接口库 `tutorial_compiler_flags`。然后使用 [target_compile_features()] 添加编译器特性 `cxx_std_11`。

??? example "点击展开查看 `TODO 4`"

    ``` cmake title="CMakeLists.txt"
    add_library(tutorial_compiler_flags INTERFACE)
    target_compile_features(tutorial_compiler_flags INTERFACE cxx_std_11)
    ```

最后，设置好界面库后，我们需要将可执行目标、`MathFunctions` 库和 `SqrtLibrary` 库链接到新的 `tutorial_compiler_flags` 库。代码将分别如下：

??? example "点击展开查看 `TODO 5`"

    ``` cmake title="CMakeLists.txt"
    target_link_libraries(Tutorial PUBLIC MathFunctions tutorial_compiler_flags)
    ```
还有

??? example "点击展开查看 `TODO 6`"

    ``` cmake title="MathFunctions/CMakeLists.txt"
    target_link_libraries(SqrtLibrary PUBLIC tutorial_compiler_flags)
    ```

以及

??? example "点击展开查看 `TODO 7`"

    ``` cmake title="MathFunctions/CMakeLists.txt"
    target_link_libraries(MathFunctions PUBLIC SqrtLibrary)
    ```

这样，我们的所有代码仍然需要 `C++ 11` 才能构建。不过请注意，有了这种方法，我们就能明确哪些目标需要特定的要求。此外，我们还在接口库中创建了一个单一的真相源。

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
[生成器表达式]: https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html#manual:cmake-generator-expressions(7)
[add_library()]: https://cmake.org/cmake/help/latest/command/add_library.html#command:add_library
[target_compile_features()]: https://cmake.org/cmake/help/latest/command/target_compile_features.html#command:target_compile_features
[target_link_libraries()]: https://cmake.org/cmake/help/latest/command/target_link_libraries.html#command:target_link_libraries
[CMAKE_CXX_STANDARD]: https://cmake.org/cmake/help/latest/variable/CMAKE_CXX_STANDARD.html#variable:CMAKE_CXX_STANDARD
[CMAKE_CXX_STANDARD_REQUIRED]: https://cmake.org/cmake/help/latest/variable/CMAKE_CXX_STANDARD_REQUIRED.html#variable:CMAKE_CXX_STANDARD_REQUIRED
[set()]: https://cmake.org/cmake/help/latest/command/set.html#command:set