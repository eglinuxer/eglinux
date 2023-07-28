---
title: 第 5 步 [安装和测试]
description: 程序编译出来可不算完，还需要可安装，更需要单元测试。
serial: CMake 工程实践指南-基础教程
---

# 第 5 步 [安装和测试]

## 练习 1 - 安装规则

通常，仅仅构建一个可执行文件是不够的，它还应该是可安装的。使用 CMake，我们可以使用 [install()] 命令指定安装规则。在 CMake 中为您的构建支持本地安装通常与指定安装位置以及要安装的目标和文件一样简单。

### 要完成的目标

安装 `Tutorial` 可执行文件和 `MathFunctions` 库。

### 参考资源

- [install()]

### 需要编辑的文件

- `MathFunctions/CMakeLists.txt`
- `CMakeLists.txt`

### 入门

起始代码在 `Step5` 目录中提供。在本练习中，完成 `TODO 1` 到 `TODO 4`。

首先，更新 `MathFunctions/CMakeLists.txt` 以将 `MathFunctions` 和 `tutorial_compiler_flags` 库安装到 `lib` 目录。在同一个文件中，指定将 `MathFunctions.h` 安装到 `include` 目录所需的安装规则。

然后，更新顶层 `CMakeLists.txt` 以将 `Tutorial` 可执行文件安装到 `bin` 目录。最后，任何头文件都应该安装到 `include` 目录中。请记住，`TutorialConfig.h` 位于 [PROJECT_BINARY_DIR] 中。

### 构建和运行

运行 [cmake] 可执行文件或 [cmake-gui] 来配置项目，然后使用您选择的构建工具构建它。

然后，从命令行使用 [cmake] 命令的 `--install` 选项（在 3.15 中引入，旧版本的 CMake 必须使用 `make install`）运行安装步骤。此步骤将安装适当的头文件、库和可执行文件。例如：

``` shell
cmake --install build
```

对于多配置工具，不要忘记使用 `--config` 参数来指定配置。

``` shell
cmake --install build --config Release
```

如果使用 `IDE`，只需构建 `INSTALL` 目标。您可以从命令行构建相同的安装目标，如下所示：

``` shell
cmake --build build --target install --config Debug
```

CMake 变量 [CMAKE_INSTALL_PREFIX] 用于确定将安装文件的根目录。如果使用 `cmake --install` 命令，可以通过 `--prefix` 参数覆盖安装前缀。例如：

``` shell
cmake --install build --prefix "/home/myuser/installdir"
```

进入安装目录并验证安装的教程是否运行。

### 解决方案

我们项目的安装规则非常简单：

- 对于 `MathFunctions`，我们希望将库和头文件分别安装到 `lib` 和 `include` 目录。
- 对于 `Tutorial` 可执行文件，我们要将可执行文件和配置的头文件分别安装到 `bin` 和 `include` 目录。

所以在 `MathFunctions/CMakeLists.txt` 的末尾我们添加：

??? example "点击展开查看 `TODO 1`"

    ``` cmake
    set(installable_libs MathFunctions tutorial_compiler_flags)
    install(
        TARGETS
            ${installable_libs}
        DESTINATION
            lib
    )
    ```

??? example "点击展开查看 `TODO 2`"

    ``` cmake
    set(installable_libs MathFunctions tutorial_compiler_flags)
    install(
        FILES
            MathFunctions.h
        DESTINATION
            include
    )
    ```

`Tutorial` 可执行文件和配置的头文件的安装规则相似。在顶级 `CMakeLists.txt` 的末尾，我们添加：

??? example "点击展开查看 `TODO 3` 和 `TODO 4`"

    ``` cmake
    install(
        TARGETS
            Tutorial
        DESTINATION
            bin
    )

    install(
        FILES
            "${PROJECT_BINARY_DIR}/TutorialConfig.h"
        DESTINATION
            include
    )
    ```

这就是创建本教程的基本本地安装所需的全部内容。

## 练习 2 - 测试支持

CTest 提供了一种轻松管理项目测试的方法。可以通过 [add_test()] 命令添加测试。尽管本教程中没有明确涵盖，但 CTest 与其他测试框架（如 [GoogleTest]）之间存在很多兼容性。

### 要完成的目标

使用 CTest 为我们的可执行文件创建单元测试。

### 参考资源

- [enable_testing()]
- [add_test()]
- [function()]
- [set_tests_properties()]
- [ctest]

### 需要编辑的文件

- `CMakeLists.txt`

### 入门

起始源代码在 `Step5` 目录中提供。在本练习中，完成 `TODO 5` 到 `TODO 9`。

首先，我们需要启用测试。接下来，开始使用 [add_test()] 向我们的项目添加测试。我们将通过添加 3 个简单测试来工作，然后您可以根据需要添加其他测试。



### 构建和运行

重建应用程序。然后，运行 [ctest] 可执行文件：`ctest -N` 和 `ctest -VV`。对于多配置生成器（例如 `Visual Studio`），必须使用 `-C <mode>` 标志指定配置类型。例如，要在调试模式下运行测试，请在构建目录（不是 `Debug` 子目录！）中运行 `ctest -C Debug -VV`。`Release` 将从相同位置执行，但使用 `-C Release`。或者，从 `IDE` 构建 `RUN_TESTS` 目标。

### 解决方案

让我们测试我们的应用程序。在顶级 `CMakeLists.txt` 文件的末尾，我们首先需要使用 [enable_testing()] 命令启用测试。

??? example "点击展开查看 `TODO 5`"

    ``` cmake
    enable_testing()
    ```

启用测试后，我们将添加一些基本测试来验证应用程序是否正常工作。首先，我们使用 [add_test()] 创建一个测试，它运行传入参数 `25` 的 `Tutorial` 可执行文件。对于此测试，我们不打算检查可执行文件的计算结果。此测试将验证应用程序是否运行、没有出现段错误或以其他方式崩溃，并且返回值为零。这是 CTest 测试的基本形式。

??? example "点击展开查看 `TODO 6`"

    ``` cmake
    add_test(NAME Runs COMMAND Tutorial 25)
    ```

接下来，让我们使用 [PASS_REGULAR_EXPRESSION] 测试属性来验证测试的输出是否包含某些字符串。在这种情况下，验证在提供的参数数量不正确时是否打印了用法消息。

??? example "点击展开查看 `TODO 7`"

    ``` cmake
    add_test(NAME Usage COMMAND Tutorial)
    set_tests_properties(Usage
        PROPERTIES
            PASS_REGULAR_EXPRESSION "Usage:.*number"
    )
    ```

我们将添加的下一个测试将验证计算值是否真的是平方根。

??? example "点击展开查看 `TODO 8`"

    ``` cmake
    add_test(NAME StandardUse COMMAND Tutorial 4)
    set_tests_properties(StandardUse
        PROPERTIES
            PASS_REGULAR_EXPRESSION "4 is 2"
    )
    ```

这个测试不足以让我们相信它适用于所有传入的值。我们应该添加更多测试来验证这一点。为了轻松添加更多测试，我们创建了一个名为 `do_test` 的函数来运行应用程序并验证计算的平方根对于给定输入是否正确。对于 `do_test` 的每次调用，都会将另一个测试添加到项目中，其中包含基于传递的参数的名称、输入和预期结果。

??? example "点击展开查看 `TODO 9`"

    ``` cmake
    function(do_test target arg result)
        add_test(NAME Comp${arg} COMMAND ${target} ${arg})
        set_tests_properties(Comp${arg}
            PROPERTIES
                PASS_REGULAR_EXPRESSION ${result}
        )
    endfunction()

    # do a bunch of result based tests
    do_test(Tutorial 4 "4 is 2")
    do_test(Tutorial 9 "9 is 3")
    do_test(Tutorial 5 "5 is 2.236")
    do_test(Tutorial 7 "7 is 2.645")
    do_test(Tutorial 25 "25 is 5")
    do_test(Tutorial -25 "-25 is (-nan|nan|0)")
    do_test(Tutorial 0.0001 "0.0001 is 0.01")
    ```


[install()]: https://cmake.org/cmake/help/latest/command/install.html#command:install
[PROJECT_BINARY_DIR]: https://cmake.org/cmake/help/latest/variable/PROJECT_BINARY_DIR.html#variable:PROJECT_BINARY_DIR
[CMAKE_INSTALL_PREFIX]: https://cmake.org/cmake/help/latest/variable/CMAKE_INSTALL_PREFIX.html#variable:CMAKE_INSTALL_PREFIX
[cmake]: https://cmake.org/cmake/help/latest/manual/cmake.1.html#manual:cmake(1)
[cmake-gui]: https://cmake.org/cmake/help/latest/manual/cmake-gui.1.html#manual:cmake-gui(1)
[ccmake]: https://cmake.org/cmake/help/latest/manual/ccmake.1.html#manual:ccmake(1)
[add_test()]: https://cmake.org/cmake/help/latest/command/add_test.html#command:add_test
[GoogleTest]: https://cmake.org/cmake/help/latest/module/GoogleTest.html#module:GoogleTest
[enable_testing()]: https://cmake.org/cmake/help/latest/command/enable_testing.html#command:enable_testing
[function()]: https://cmake.org/cmake/help/latest/command/function.html#command:function
[set_tests_properties()]: https://cmake.org/cmake/help/latest/command/set_tests_properties.html#command:set_tests_properties
[ctest]: https://cmake.org/cmake/help/latest/manual/ctest.1.html#manual:ctest(1)
[PASS_REGULAR_EXPRESSION]: https://cmake.org/cmake/help/latest/prop_test/PASS_REGULAR_EXPRESSION.html#prop_test:PASS_REGULAR_EXPRESSION
