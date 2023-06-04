---
title: 第 1 步 [一个简单的起点]
description: 学习如何从零开始创建一个 CMake 项目，并完成 3 个小练习。
serial: CMake 工程实践指南-基础教程
---

# 第 1 步 [一个简单的起点]

我从哪里开始使用 CMake？此步骤将介绍 CMake 的一些基本语法、命令和变量。在介绍这些概念时，我们将完成三个练习并创建一个简单的 CMake 项目。

此步骤中的每个练习都将从一些背景信息开始。然后，列出`要完成的目标`和`参考资源`。`需要编辑的文件`部分中的每个文件都位于本教程源码的 `Step1` 目录中，并包含一个或多个 `TODO` 注释。每个 `TODO` 代表要更改或添加的一两行代码。 `TODO` 旨在按数字顺序完成，首先完成 `TODO 1`，然后完成 `TODO 2`，依此类推。`入门`部分将提供一些有用的提示并指导您完成练习。然后`构建和运行`部分将逐步介绍如何构建和测试练习。最后，在每个练习结束时讨论预期的`解决方案`。

另请注意，本教程中的每个步骤都是下一步的基础。例如，`第 2 步`的起始代码是`第 1 步`完整解决方案的代码。

## 练习 1 - 构建一个基本项目

最基本的 CMake 项目是从单个源代码文件构建的可执行文件。对于像这样的简单项目，只需要一个包含三个命令的 `CMakeLists.txt` 文件。

!!! note "提示"

    尽管 CMake 支持大写、小写和混合大小写命令，但首选小写命令，首选小写命令将在整个教程中使用。

任何项目的最顶层 `CMakeLists.txt` 必须首先使用 [cmake_minimum_required()] 命令指定最低 CMake 版本。这会出发 CMake 策略设置并确保以下 CMake 命令使用兼容版本的 CMake 运行。

要启动一个项目，我们使用 [project()] 命令来设置项目名称。每个项目都需要此调用，并且应在 [cmake_minimum_required()] 之后立即调用。正如我们稍后将看到的，此命令还可用于指定其他项目级别的信息，例如语言或版本号。

最后，[add_executable()] 命令告诉 CMake 使用指定的源代码文件创建可执行文件。

### 要完成的目标

了解如何创建简单的 CMake 项目。

### 参考资源

- [add_executable()]
- [cmake_minimum_required()]
- [project()]

### 需要编辑的文件

- `CMakeLists.txt`

### 入门

`tutorial.cxx` 的源代码在 `Step1` 目录中提供，可用于计算数字的平方根。此文件不需要在此步骤中进行编辑。

在同一目录中有一个需要更改的 `CMakeLists.txt` 文件。从 `TODO 1` 开始，完成至 `TODO 3`。

### 构建和运行

一旦 `TODO 1` 到 `TODO 3` 完成，我们就可以构建和运行我们的项目了！首先，运行 [cmake] 可执行文件或 [cmake-gui] 来配置项目，然后使用您选择的构建工具构建它。

首先命令行进入 `Step1` 目录。

依次执行如下命令：

``` shll
cmake -S . -B build
cmake --build build
```

上述两个命令对项目进行配置和编译，如果没有报错，可以执行下面的命令，验证编译出的二进制可执行程序。

``` shell
./build/Tutorial 4294967296
./build/Tutorial 10
./build/Tutorial
```

### 解决方案

??? example "点击展开查看 `TODO 1`"

    ``` cmake title="CMakeLists.txt"
    cmake_minimum_required(VERSION 3.10)
    ```

??? example "点击展开查看 `TODO 2`"

    ``` cmake title="CMakeLists.txt"
    project(Tutorial)
    ```

??? example "点击展开查看 `TODO 3`"

    ``` cmake title="CMakeLists.txt"
    add_executable(Tutorial tutorial.cxx)
    ```

## 练习 2 - 指定 C++ 标准

CMake 有一些特殊变量，这些变量要么在幕后创建，要么在项目代码设置时对 CMake 有意义。许多这些变量以 `CMAKE_` 开头。在为您的项目创建变量时避免这种命名约定。其中两个特殊的用户可设置变量是 [CMAKE_CXX_STANDARD] 和 [CMAKE_CXX_STANDARD_REQUIRED]。这些可以一起使用来指定构建项目所需的 C++ 标准。

### 要完成的目标

添加 C++ 11 支持

### 参考资源

- [CMAKE_CXX_STANDARD]
- [CMAKE_CXX_STANDARD_REQUIRED]
- [set()]

### 需要编辑的文件

- `CMakeLists.txt`
- `tutorial.cxx`

### 入门

继续编辑 `Step1` 目录中的文件。从 `TODO 4` 开始，完成 `TODO 6`。

首先，通过添加需要 `C++ 11` 的功能来编辑 `tutorial.cxx`。然后更新 `CMakeLists.txt` 以要求 `C++ 11`。

### 构建和运行

让我们再次构建我们的项目。由于我们已经为[练习 1](#1-) 创建了构建目录并运行了 CMake，因此我们可以跳到构建步骤：

``` shell
cmake --build build
```

现在我们可以尝试使用新构建的 `Tutorial`，使用与之前相同的命令：

``` shell
./build/Tutorial 4294967296
./build/Tutorial 10
./build/Tutorial
```

### 解决方案

我们首先通过将 `tutorial.cxx` 中的 `atof` 替换为 `std::stod` 来向我们的项目添加一些 `C++ 11` 功能。这看起来像下面这样：

??? example "点击展开查看 `TODO 4`"

    ``` cpp title="tutorial.cxx"
    const double inputValue = std::stod(argv[1]);
    ```

要完成 `TODO 5`，只需删除 `#include <cstdlib>`。

我们需要在 CMake 代码中明确声明它应该使用正确的标志。在 CMake 中启用对特定 C++ 标准的支持的一种方法是使用 [CMAKE_CXX_STANDARD] 变量。对于本教程，将 `CMakeLists.txt` 文件中的 [CMAKE_CXX_STANDARD] 变量设置为 `11`，将 [CMAKE_CXX_STANDARD_REQUIRED] 设置为 `TRUE`。确保在对 [add_executable()] 的调用之上添加 [CMAKE_CXX_STANDARD] 声明。

??? example "点击展开查看 `TODO 6`"

    ``` cmake title="CMakeLists.txt"
    set(CMAKE_CXX_STANDARD 11)
    set(CMAKE_CXX_STANDARD_REQUIRED True)
    ```

## 练习 3 - 添加版本号和配置头文件

有时，在您的 `CMakelists.txt` 文件中定义的变量在您的源代码中也可用可能会很有用。在这种情况下，我们可能想打印项目版本。

实现此目的的一种方法是使用已配置的头文件。我们创建一个输入文件，其中包含一个或多个要替换的变量。这些变量有特殊的语法，看起来像 `@VAR@`。然后，我们使用 [configure_file()] 命令将输入​​文件复制到给定的输出文件，并将这些变量替换为 `CMakelists.txt` 文件中 `VAR` 的当前值。

虽然我们可以直接在源代码中编辑版本，但首选使用此功能，因为它创建了单一的真实来源并避免了重复。

### 要完成的目标

定义并报告项目的版本号。

### 参考资源

- [<PROJECT-NAME\>_VERSION_MAJOR](https://cmake.org/cmake/help/latest/variable/PROJECT-NAME_VERSION_MAJOR.html#variable:%3CPROJECT-NAME%3E_VERSION_MAJOR)
- [<PROJECT-NAME\>_VERSION_MINOR](https://cmake.org/cmake/help/latest/variable/PROJECT-NAME_VERSION_MINOR.html#variable:%3CPROJECT-NAME%3E_VERSION_MINOR)
- [configure_file()]
- [target_include_directories()]

### 需要编辑的文件

- `CMakeLists.txt`
- `tutorial.cxx`

### 入门

继续编辑 `Step1` 中的文件。从 `TODO 7` 开始，完成至 `TODO 12`。在本练习中，我们首先在 `CMakeLists.txt` 中添加项目版本号。在同一个文件中，使用 [configure_file()] 将给定的输入文件复制到输出文件，并替换输入文件内容中的一些变量值。

接下来，创建一个输入头文件 `TutorialConfig.h.in` 定义版本号，它将接受从 [configure_file()] 传递的变量。

最后，更新 `tutorial.cxx` 以打印出其版本号。

### 构建和运行

让我们再次构建我们的项目。和以前一样，我们已经创建了一个构建目录并运行了 CMake，因此我们可以跳到构建步骤：

``` shell
cmake --build build
```

验证在不带任何参数运行可执行文件时现在是否报告了版本号。

### 解决方案

在本练习中，我们通过打印版本号来改进我们的可执行文件。虽然我们可以专门在源代码中执行此操作，但使用 `CMakeLists.txt` 可以让我们维护版本号的单一数据源。

首先，我们修改 `CMakeLists.txt` 文件以使用 [project()] 命令设置项目名称和版本号。当调用 [project()] 命令时，CMake 在幕后定义了 `Tutorial_VERSION_MAJOR` 和 `Tutorial_VERSION_MINOR`。

??? example "点击展开查看 `TODO 7`"

    ``` cmake title="CMakeLists.txt"
    project(Tutorial VERSION 1.0)
    ```

然后我们使用 [configure_file()] 复制输入文件并替换指定的 CMake 变量：

??? example "点击展开查看 `TODO 8`"

    ``` cmake title="CMakeLists.txt"
    configure_file(TutorialConfig.h.in TutorialConfig.h)
    ```

由于配置文件将写入项目构建目录，我们必须将该目录添加到路径列表中以搜索包含文件。

!!! note "提示"

    在本教程中，我们将交替引用项目构建目录和项目二进制目录。这些是相同的，并不意味着引用 `bin/` 目录。

我们使用 [target_include_directories()] 来指定可执行目标应该在哪里寻找包含文件。

??? example "点击展开查看 `TODO 9`"

    ``` cmake title="CMakeLists.txt"
    target_include_directories(Tutorial
        PUBLIC
            "${PROJECT_BINARY_DIR}"
    )
    ```

`TutorialConfig.h.in` 是要配置的输入头文件。当从我们的 `CMakeLists.txt` 调用 [configure_file()] 时，`@Tutorial_VERSION_MAJOR@` 和 `@Tutorial_VERSION_MINOR@` 的值将替换为 `TutorialConfig.h` 中项目的相应版本号。

??? example "点击展开查看 `TODO 10`"

    ``` cpp title="TutorialConfig.h.in"
    // the configured options and settings for Tutorial
    #define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
    #define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
    ```

接下来，我们需要修改 `tutorial.cxx` 以包含配置的头文件 `TutorialConfig.h`。

??? example "点击展开查看 `TODO 11`"

    ``` cpp title="tutorial.cxx"
    #include "TutorialConfig.h"
    ```

最后，我们通过更新 `tutorial.cxx` 打印出可执行文件名称和版本号，如下所示：

??? example "点击展开查看 `TODO 12`"

    ``` cpp title="tutorial.cxx"
    if (argc < 2) {
        // report version
        std::cout << argv[0] << " Version " << Tutorial_VERSION_MAJOR << "." << Tutorial_VERSION_MINOR << std::endl;
        std::cout << "Usage: " << argv[0] << " number" << std::endl;
        return 1;
    }

    ```


[cmake_minimum_required()]: https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html#command:cmake_minimum_required
[project()]: https://cmake.org/cmake/help/latest/command/project.html#command:project
[add_executable()]: https://cmake.org/cmake/help/latest/command/add_executable.html#command:add_executable
[cmake]: https://cmake.org/cmake/help/latest/manual/cmake.1.html#manual:cmake(1)
[cmake-gui]: https://cmake.org/cmake/help/latest/manual/cmake-gui.1.html#manual:cmake-gui(1)
[CMAKE_CXX_STANDARD]: https://cmake.org/cmake/help/latest/variable/CMAKE_CXX_STANDARD.html#variable:CMAKE_CXX_STANDARD
[CMAKE_CXX_STANDARD_REQUIRED]: https://cmake.org/cmake/help/latest/variable/CMAKE_CXX_STANDARD_REQUIRED.html#variable:CMAKE_CXX_STANDARD_REQUIRED
[set()]: https://cmake.org/cmake/help/latest/command/set.html#command:set
[configure_file()]: https://cmake.org/cmake/help/latest/command/configure_file.html#command:configure_file
[target_include_directories()]: https://cmake.org/cmake/help/latest/command/target_include_directories.html#command:target_include_directories