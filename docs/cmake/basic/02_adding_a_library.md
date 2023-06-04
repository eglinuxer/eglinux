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

- `CMakeLists.txt`
- `tutorial.cxx`
- `MathFunctions/CMakeLists.txt`

### 入门

在本练习中，我们将向我们的项目添加一个库，其中包含我们自己用于计算数字平方根的实现。然后可执行文件可以使用这个库而不是编译器提供的标准平方根函数。

对于本教程，我们会将库放入名为 `MathFunctions` 的子目录中。该目录已包含头文件 `MathFunctions.h` 和源文件 `mysqrt.cxx`。我们不需要修改这些文件中的任何一个。源文件有一个名为 `mysqrt` 的函数，它提供与编译器的 `sqrt` 函数类似的功能。

在 `Step2` 目录中，从 `TODO 1` 开始，完成 `TODO 6`。

首先，填写 `MathFunctions` 子目录下的一行 `CMakeLists.txt`。

接下来，编辑顶级 `CMakeLists.txt`。

最后，在 `tutorial.cxx` 中使用新创建的 `MathFunctions` 库。

### 构建和运行

运行 [cmake] 可执行文件或 [cmake-gui] 来配置项目，然后使用您选择的构建工具构建它。

首先命令行进入 `Step2` 目录。

依次执行如下命令：

``` shll
cmake -S . -B build
cmake --build build
```

尝试使用新建的 `Tutorial` 并确保它仍然生成准确的平方根值。

### 解决方案

在 `MathFunctions` 目录的 `CMakeLists.txt` 文件中，我们使用 [add_library()] 创建了一个名为 `MathFunctions` 的库目标。库的源文件作为参数传递给 [add_library()]。这看起来像下面一行：

??? example "点击展开查看 `TODO 1`"

    ``` cmake title="MathFunctions/CMakeLists.tx"
    add_library(MathFunctions mysqrt.cxx)
    ```

为了使用新库，我们将在顶级 `CMakeLists.txt` 文件中添加一个 [add_subdirectory()] 调用，以便构建该库。

??? example "点击展开查看 `TODO 2`"

    ``` cmake title="CMakeLists.txt"
    add_subdirectory(MathFunctions)
    ```

接下来，使用 [target_link_libraries()] 将新库目标链接到可执行目标。

??? example "点击展开查看 `TODO 3`"

    ``` cmake title="CMakeLists.txt"
    target_link_libraries(Tutorial PUBLIC MathFunctions)
    ```

最后我们需要指定库的头文件位置。修改 [target_include_directories()] 以将 `MathFunctions` 子目录添加为包含目录，以便可以找到 `MathFunctions.h` 头文件。

??? example "点击展开查看 `TODO 4`"

    ``` cmake title="CMakeLists.txt"
    target_include_directories(Tutorial
        PUBLIC
            "${PROJECT_BINARY_DIR}"
            "${PROJECT_SOURCE_DIR}/MathFunctions"
    )
    ```

现在让我们使用我们的库。在 `tutorial.cxx` 中，包含 `MathFunctions.h`：

??? example "点击展开查看 `TODO 5`"

    ``` cpp title="tutorial.cxx"
    #include "MathFunctions.h"
    ```

最后，将 `sqrt` 替换为我们的库函数 `mysqrt`。

??? example "点击展开查看 `TODO 6`"

    ``` cpp title="tutorial.cxx"
    const double outputValue = mysqrt(inputValue);
    ```

## 练习2 - 将库设置可选

现在让我们将 `MathFunctions` 库设为可选。虽然对于本教程来说确实没有必要这样做，但对于较大的项目来说这是很常见的事情。

CMake 可以使用 [option()] 命令执行此操作。这为用户提供了一个变量，他们可以在配置他们的 `cmake` 构建时更改该变量。此设置将存储在缓存中，因此用户无需在每次在构建目录上运行 CMake 时都设置该值。

### 要完成的目标

添加选项控制是否构建 `MathFunctions`。

### 参考资源

- [if()]
- [list()]
- [option()]
- [cmakedefine]

### 需要编辑的文件

- `CMakeLists.txt`
- `tutorial.cxx`
- `TutorialConfig.h.in`

### 入门

从[练习 1](#1-) 的结果文件开始。完成 `TODO 7` 到 `TODO 13`。

首先在顶级 `CMakeLists.txt` 文件中使用 [option()] 命令创建一个变量 `USE_MYMATH`。在同一个文件中，使用该选项来确定是否构建和使用 `MathFunctions` 库。

然后，更新 `tutorial.cxx` 和 `TutorialConfig.h.in` 以使用 `USE_MYMATH`。

### 构建和运行

由于我们已经从[练习 1](#1-) 中配置了构建目录，我们可以通过简单地调用以下命令来重建：

``` shell
cmake --build build
```

接下来，对几个数字运行 `Tutorial` 可执行文件以验证它是否仍然正确。

现在让我们将 `USE_MYMATH` 的值更新为 `OFF`。如果您在终端中，最简单的方法是使用 [cmake-gui] 或 [ccmake]。或者，如果您想从命令行更改选项，请尝试：

``` shell
cmake -S . -B build -DUSE_MYMATH=OFF
cmake --build build
```
然后，再次运行可执行文件以确保它仍然可以在 `USE_MYMATH` 设置为 `OFF` 的情况下工作。哪个函数提供更好的结果，`sqrt` 还是 `mysqrt`？

### 解决方案

第一步是向顶级 `CMakeLists.txt` 文件添加一个选项。此选项将显示在 [cmake-gui] 和 [ccmake] 中，默认值为 `ON`，用户可以更改。

??? example "点击展开查看 `TODO 7`"

    ``` cmake title="CMakeLists.txt"
    option(USE_MYMATH "Use tutorial provided math implementation" ON)
    ```

接下来，使构建和链接 `MathFunctions` 库成为条件。

首先为我们的项目创建可选库目标的 [list()]。目前，它只是 `MathFunctions`。让我们将列表命名为 `EXTRA_LIBS`。

同样，我们需要为我们称之为 `EXTRA_INCLUDES` 的可选包含创建一个 [list()]。在此列表中，我们将附加我们的库所需的头文件的路径。

接下来，创建一个 [if()] 语句来检查 `USE_MYMATH` 的值。在 [if()] 块内，将[练习 1](#1-) 中的 [add_subdirectory()] 命令与其他 [list()] 命令放在一起。

当 `USE_MYMATH` 为 `ON` 时，将生成列表并将其添加到我们的项目中。当 `USE_MYMATH` 为 `OFF` 时，列表保持为空。通过这种策略，我们允许用户切换 `USE_MYMATH` 来操纵构建中使用的库。

顶级 `CMakeLists.txt` 文件现在如下所示：

??? example "点击展开查看 `TODO 8`"

    ``` cmake title="CMakeLists.txt"
    if(USE_MYMATH)
        add_subdirectory(MathFunctions)
        list(APPEND EXTRA_LIBS MathFunctions)
        list(APPEND EXTRA_INCLUDES "${PROJECT_SOURCE_DIR}/MathFunctions")
    endif()
    ```

现在我们有了这两个列表，我们需要更新 [target_link_libraries()] 和 [target_include_directories()] 来使用它们。更改它们非常简单。

对于 [target_link_libraries()]，我们将写出的库名称替换为 `EXTRA_LIBS`。这看起来像下面这样：

??? example "点击展开查看 `TODO 9`"

    ``` cmake title="CMakeLists.txt"
    target_link_libraries(Tutorial
        PUBLIC
            ${EXTRA_LIBS}
    )
    ```

然后，我们对 [target_include_directories()] 和 `EXTRA_INCLUDES` 做同样的事情。

??? example "点击展开查看 `TODO 10`"

    ``` cmake title="CMakeLists.txt"
    target_include_directories(Tutorial
        PUBLIC
            "${PROJECT_BINARY_DIR}"
            ${EXTRA_INCLUDES}
    )
    ```

!!! note "注意"

    请注意，这是处理许多组件时的经典方法。我们将在本教程的[第 3 步](./03_adding_usage_requirements_for_a_library.md)中介绍现代方法。

对源代码的相应更改非常简单。首先，在 `tutorial.cxx` 中，如果定义了 `USE_MYMATH`，我们将包含 `MathFunctions.h` 头文件。

??? example "点击展开查看 `TODO 11`"

    ``` cpp title="tutorial.cxx"
    #ifdef USE_MYMATH
        #include "MathFunctions.h"
    #endif
    ```

然后，在同一个文件中，我们让 `USE_MYMATH` 控制使用哪个平方根函数：

??? example "点击展开查看 `TODO 12`"

    ``` cpp title="tutorial.cxx"
    #ifdef USE_MYMATH
        const double outputValue = mysqrt(inputValue);
    #else
        const double outputValue = sqrt(inputValue);
    #endif
    ```

由于源代码现在需要 `USE_MYMATH`，我们可以使用以下行将其添加到 `TutorialConfig.h.in`：

??? example "点击展开查看 `TODO 13`"

    ``` cpp title="TutorialConfig.h.in"
    #cmakedefine USE_MYMATH
    ```

通过这些更改，我们的库现在对于构建和使用它的任何人来说都是完全可选的。

??? question "思考题"

    为什么在 `USE_MYMATH` 选项之后配置 `TutorialConfig.h.in` 很重要？如果我们将两者倒置会发生什么？

??? success "答案"

    我们配置之后是因为 `TutorialConfig.h.in` 使用了 `USE_MYMATH` 的值。如果我们在调用 [option()] 之前配置文件，我们将不会使用 `USE_MYMATH` 的预期值。

[add_library()]: https://cmake.org/cmake/help/latest/command/add_library.html#command:add_library
[add_subdirectory()]: https://cmake.org/cmake/help/latest/command/add_subdirectory.html#command:add_subdirectory
[target_include_directories()]: https://cmake.org/cmake/help/latest/command/target_include_directories.html#command:target_include_directories
[target_link_libraries()]: https://cmake.org/cmake/help/latest/command/target_link_libraries.html#command:target_link_libraries
[PROJECT_SOURCE_DIR]: https://cmake.org/cmake/help/latest/variable/PROJECT_SOURCE_DIR.html#variable:PROJECT_SOURCE_DIR
[cmake]: https://cmake.org/cmake/help/latest/manual/cmake.1.html#manual:cmake(1)
[cmake-gui]: https://cmake.org/cmake/help/latest/manual/cmake-gui.1.html#manual:cmake-gui(1)
[ccmake]: https://cmake.org/cmake/help/latest/manual/ccmake.1.html#manual:ccmake(1)
[option()]: https://cmake.org/cmake/help/latest/command/option.html#command:option
[if()]: https://cmake.org/cmake/help/latest/command/if.html#command:if
[list()]: https://cmake.org/cmake/help/latest/command/list.html#command:list
[cmakedefine]: https://cmake.org/cmake/help/latest/command/configure_file.html#command:configure_file