# 第 8 步 [自定义命令和生成文件]

假设，为了本教程的目的，我们决定永远不想使用平台 `log` 和 `exp` 函数，而是想生成一个预计算值表以在 `mysqrt` 函数中使用。在本节中，我们将创建表作为构建过程的一部分，然后将该表编译到我们的应用程序中。

首先，让我们删除 `MathFunctions/CMakeLists.txt` 中对 `log` 和 `exp` 函数的检查。然后从 `mysqrt.cxx` 中删除对 `HAVE_LOG` 和 `HAVE_EXP` 的检查。同时，我们可以去掉 `#include <cmath>`。

在 `MathFunctions` 子目录中，提供了一个名为 `MakeTable.cxx` 的新源文件来生成表格。

查看文件后，我们可以看到该表是作为有效的 C++ 代码生成的，并且输出文件名作为参数传入。

下一步是将适当的命令添加到 `MathFunctions/CMakeLists.txt` 文件以构建 `MakeTable` 可执行文件，然后将其作为构建过程的一部分运行。需要一些命令来完成此操作。

首先，在 `MathFunctions/CMakeLists.txt` 的顶部，添加 `MakeTable` 的可执行文件，就像添加任何其他可执行文件一样。

``` cmake title="MathFunctions/CMakeLists.txt"
add_executable(MakeTable MakeTable.cxx)
```

然后我们添加一个自定义命令，指定如何通过运行 `MakeTable` 来生成 `Table.h`。

``` cmake title="MathFunctions/CMakeLists.txt"
add_custom_command(
    OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/Table.h
    COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/Table.h
    DEPENDS MakeTable
)
```

接下来我们要让 CMake 知道 `mysqrt.cxx` 依赖于生成的文件 `Table.h`。这是通过将生成的 `Table.h` 添加到库 `MathFunctions` 的源列表中来完成的。

``` cmake title="MathFunctions/CMakeLists.txt"
add_library(MathFunctions
    mysqrt.cxx
    ${CMAKE_CURRENT_BINARY_DIR}/Table.h
)
```

我们还必须将当前二进制目录添加到包含目录列表中，以便 `table.h` 可以被 `mysqrt.cxx` 找到和包含。

``` cmake title="MathFunctions/CMakeLists.txt"
target_include_directories(MathFunctions
    INTERFACE
        ${CMAKE_CURRENT_SOURCE_DIR}
    PRIVATE
        ${CMAKE_CURRENT_BINARY_DIR}
)

# link our compiler flags interface library
target_link_libraries(MathFunctions tutorial_compiler_flags)
```

现在让我们使用生成的表。首先，修改 `mysqrt.cxx` 以包含 `Table.h`。接下来，我们可以重写 `mysqrt` 函数来使用该表：

``` cpp title="MathFunctions/mysqrt.cxx"
double mysqrt(double x)
{
    if (x <= 0) {
        return 0;
    }

    // use the table to help find an initial value
    double result = x;
    if (x >= 1 && x < 10) {
        std::cout << "Use the table to help find an initial value " << std::endl;
        result = sqrtTable[static_cast<int>(x)];
    }

    // do ten iterations
    for (int i = 0; i < 10; ++i) {
        if (result <= 0) {
            result = 0.1;
        }
        double delta = x - (result * result);
        result = result + 0.5 * delta / result;
        std::cout << "Computing sqrt of " << x << " to be " << result << std::endl;
    }

    return result;
}
```

运行 [cmake] 可执行文件或 [cmake-gui] 来配置项目，然后使用您选择的构建工具构建它。

构建此项目时，它将首先构建 `MakeTable` 可执行文件。然后它将运行 `MakeTable` 以生成 `Table.h`。最后，它将编译包含 `Table.h` 的 `mysqrt.cxx` 以生成 `MathFunctions` 库。

运行 `Tutorial` 可执行文件并验证它是否正在使用该表。


[cmake]: https://cmake.org/cmake/help/latest/manual/cmake.1.html#manual:cmake(1)
[cmake-gui]: https://cmake.org/cmake/help/latest/manual/cmake-gui.1.html#manual:cmake-gui(1)