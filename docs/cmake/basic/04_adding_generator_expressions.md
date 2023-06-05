---
title: 第 4 步 [添加生成表达式]
description: 什么是生成器表达式？生成器表达式如何使用？
serial: CMake 工程实践指南-基础教程
---

# 第 4 步 [添加生成表达式]

[生成器表达式]在构建系统生成期间进行评估，以生成特定于每个构建配置的信息。

许多目标属性的上下文中允许使用[生成器表达式]，例如 [LINK_LIBRARIES]、[INCLUDE_DIRECTORIES]、[COMPILE_DEFINITIONS] 等。它们也可以在使用命令填充这些属性时使用，例如 [target_link_libraries()]、[target_include_directories()]、[target_compile_definitions()] 等。

[生成器表达式]可用于启用条件链接、编译时使用的条件定义、条件包含目录等。这些条件可能基于构建配置、目标属性、平台信息或任何其他可查询信息。

有不同类型的[生成器表达式]，包括逻辑表达式、信息表达式和输出表达式。

逻辑表达式用于创建条件输出。基本表达式是 `0` 和 `1` 表达式。`$<0:...>` 生成空字符串，`<1:...>` 生成 `...` 的内容。它们也可以嵌套。

##  练习 1 - 使用接口库设定 C++ 标准

在使用[生成器表达式]之前，让我们重构现有代码以使用 `INTERFACE` 库。我们将在下一步中使用该库来演示[生成器表达式]的常见用法。

### 要完成的目标

添加一个 `INTERFACE` 库目标以指定所需的 `C++` 标准。

### 参考资源

- [add_library()]
- [target_compile_features()]
- [target_link_libraries()]

### 需要编辑的文件

- `CMakeLists.txt`
- `MathFunctions/CMakeLists.txt`

### 入门

在本练习中，我们将重构代码以使用 `INTERFACE` 库来指定 `C++` 标准。

起始源代码在 `Step4` 目录中提供。在本练习中，完成 `TODO 1` 到 `TODO 3`。

首先编辑顶级 `CMakeLists.txt` 文件。构造一个名为 `tutorial_compiler_flags` 的 `INTERFACE` 库目标，并将 `cxx_std_11` 指定为目标编译器功能。

修改 `CMakeLists.txt` 和 `MathFunctions/CMakeLists.txt`，以便所有目标都具有对 `tutorial_compiler_flags` 的 [target_link_libraries()] 调用。

### 构建和运行

运行 [cmake] 可执行文件或 [cmake-gui] 来配置项目，然后使用您选择的构建工具或使用 `cmake --build` 构建它。从构建目录。

``` shell
cmake -S . -B build
cmake --build build
```

接下来，使用新构建的 `Tutorial` 并验证它是否按预期工作。

### 解决方案

让我们更新上一步中的代码，以使用接口库来设置我们的 `C++` 要求。

首先，我们需要删除对变量 [CMAKE_CXX_STANDARD] 和 [CMAKE_CXX_STANDARD_REQUIRED] 的两个 [set()] 调用。具体删除的行如下：

``` cmake title="CMakeLists.txt"
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)
```

接下来，我们需要创建一个接口库，`tutorial_compiler_flags`。然后使用 [target_compile_features()] 添加编译器功能 `cxx_std_11`。

??? example "点击展开查看 `TODO 1`"

    ``` cmake title="CMakeLists.txt"
    add_library(tutorial_compiler_flags INTERFACE)
    target_compile_features(tutorial_compiler_flags
        INTERFACE
            cxx_std_11
    )
    ```

最后，设置好接口库后，我们需要将可执行 Target 和 `MathFunctions` 库链接到新的 `tutorial_compiler_flags` 库。相应地，代码将如下所示：

??? example "点击展开查看 `TODO 2`"

    ``` cmake title="CMakeLists.txt"
    target_link_libraries(Tutorial
        PUBLIC
            ${EXTRA_LIBS}
            tutorial_compiler_flags
    )
    ```

??? example "点击展开查看 `TODO 3`"

    ``` cmake title="MathFunctions/CMakeLists.txt"
    target_link_libraries(MathFunctions tutorial_compiler_flags)
    ```

有了这个，我们所有的代码仍然需要 `C++ 11` 来构建。请注意，尽管使用此方法，它使我们能够确定哪些目标获得特定要求。此外，我们在界面库中创建了单一事实来源。

## 练习 2 - 使用生成器表达式添加编译器警告标志

[生成器表达式]的一个常见用法是有条件地添加编译器标志，例如用于语言级别或警告的标志。一个很好的模式是将此信息关联到允许此信息传播的 `INTERFACE` 目标。

### 要完成的目标

在构建时添加编译器警告标志，但不为已安装的版本添加。

### 参考资源

- [cmake-generator-expressions(7)]
- [cmake_minimum_required()]
- [set()]
- [target_compile_options()]

### 需要编辑的文件

- `CMakeLists.txt`

### 入门

从[练习 1](#1-c) 的结果文件开始。完成 `TODO 4` 到 `TODO 7`。

首先，在顶级 `CMakeLists.txt` 文件中，我们需要将 [cmake_minimum_required()] 设置为 `3.15`。在本练习中，我们将使用 `CMake 3.15` 中引入的[生成器表达式]。

接下来，我们为项目添加所需的编译器警告标志。由于警告标志因编译器而异，我们使用 `COMPILE_LANG_AND_ID` 生成器表达式来控制给定语言和一组编译器 `ID` 应用哪些标志。

### 构建和运行

由于我们已经从[练习 1](#1-c) 中配置了构建目录，因此只需调用以下命令来重建我们的代码：

``` shell
cmake --build build
```

### 解决方案

更新 [cmake_minimum_required()] 以要求至少 CMake 版本 `3.15`：

??? example "点击展开查看 `TODO 4`"

    ``` cmake title="CMakeLists.txt"
    cmake_minimum_required(VERSION 3.15)
    ```

接下来我们确定我们的系统当前使用哪个编译器来构建，因为警告标志因我们使用的编译器而异。这是通过 `COMPILE_LANG_AND_ID` [生成器表达式]完成的。我们将结果设置在变量 `gcc_like_cxx` 和 `msvc_cxx` 中，如下所示：

??? example "点击展开查看 `TODO 5`"

    ``` cmake title="CMakeLists.txt"
    set(gcc_like_cxx "$<COMPILE_LANG_AND_ID:CXX,ARMClang,AppleClang,Clang,GNU,LCC>")
    set(msvc_cxx "$<COMPILE_LANG_AND_ID:CXX,MSVC>")
    ```

接下来，我们为项目添加所需的编译器警告标志。使用我们的变量 `gcc_like_cxx` 和 `msvc_cxx`，我们可以使用另一个[生成器表达式]仅在变量为真时应用相应的标志。我们使用 [target_compile_options()] 将这些标志应用到我们的界面库中。

??? example "点击展开查看 `TODO 6`"

    ``` cmake title="CMakeLists.txt"
    target_compile_options(tutorial_compiler_flags
        INTERFACE
            "$<${gcc_like_cxx}:-Wall;-Wextra;-Wshadow;-Wformat=2;-Wunused>"
            "$<${msvc_cxx}:-W3>"
    )
    ```

最后，我们只希望在构建期间使用这些警告标志。我们已安装项目的消费者不应继承我们的警告标志。为了指定这一点，我们使用 `BUILD_INTERFACE` 条件将标志包装在生成器表达式中。生成的完整代码如下所示：

??? example "点击展开查看 `TODO 7`"

    ``` cmake title="CMakeLists.txt"
    target_compile_options(tutorial_compiler_flags
        INTERFACE
        "$<${gcc_like_cxx}:$<BUILD_INTERFACE:-Wall;-Wextra;-Wshadow;-Wformat=2;-Wunused>>"
        "$<${msvc_cxx}:$<BUILD_INTERFACE:-W3>>"
    )
    ```


[生成器表达式]: https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html#manual:cmake-generator-expressions(7)
[LINK_LIBRARIES]: https://cmake.org/cmake/help/latest/prop_tgt/LINK_LIBRARIES.html#prop_tgt:LINK_LIBRARIES
[INCLUDE_DIRECTORIES]: https://cmake.org/cmake/help/latest/prop_tgt/INCLUDE_DIRECTORIES.html#prop_tgt:INCLUDE_DIRECTORIES
[COMPILE_DEFINITIONS]: https://cmake.org/cmake/help/latest/prop_tgt/COMPILE_DEFINITIONS.html#prop_tgt:COMPILE_DEFINITIONS
[target_link_libraries()]: https://cmake.org/cmake/help/latest/command/target_link_libraries.html#command:target_link_libraries
[target_include_directories()]: https://cmake.org/cmake/help/latest/command/target_include_directories.html#command:target_include_directories
[target_compile_definitions()]: https://cmake.org/cmake/help/latest/command/target_compile_definitions.html#command:target_compile_definitions
[add_library()]: https://cmake.org/cmake/help/latest/command/add_library.html#command:add_library
[target_compile_features()]: https://cmake.org/cmake/help/latest/command/target_compile_features.html#command:target_compile_features
[CMAKE_CXX_STANDARD]: https://cmake.org/cmake/help/latest/variable/CMAKE_CXX_STANDARD.html#variable:CMAKE_CXX_STANDARD
[CMAKE_CXX_STANDARD_REQUIRED]: https://cmake.org/cmake/help/latest/variable/CMAKE_CXX_STANDARD_REQUIRED.html#variable:CMAKE_CXX_STANDARD_REQUIRED
[cmake-generator-expressions(7)]: https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html#manual:cmake-generator-expressions(7)
[cmake_minimum_required()]: https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html#command:cmake_minimum_required
[target_compile_options()]: https://cmake.org/cmake/help/latest/command/target_compile_options.html#command:target_compile_options
[set()]: https://cmake.org/cmake/help/latest/command/set.html#command:set
[cmake]: https://cmake.org/cmake/help/latest/manual/cmake.1.html#manual:cmake(1)
[cmake-gui]: https://cmake.org/cmake/help/latest/manual/cmake-gui.1.html#manual:cmake-gui(1)
[ccmake]: https://cmake.org/cmake/help/latest/manual/ccmake.1.html#manual:ccmake(1)