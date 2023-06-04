# 第 7 步 [添加系统分析]

让我们考虑向我们的项目添加一些代码，这些代码取决于目标平台可能没有的功能。对于这个例子，我们将添加一些代码，这取决于目标平台是否有 `log` 和 `exp` 函数。当然几乎每个平台都有这些功能，但在本教程中假设它们并不常见。

## 练习 1 - 评估依赖项可用性

### 要完成的目标

根据可用的系统依赖项更改实现。

### 参考资源

- [CheckCXXSourceCompiles]
- [target_compile_definitions()]

### 需要编辑的文件

- `MathFunctions/CMakeLists.txt`
- `MathFunctions/mysqrt.cxx`

### 入门

起始源代码在 `Step7` 目录中提供。在本练习中，完成 `TODO 1` 到 `TODO 5`。

首先编辑 `MathFunctions/CMakeLists.txt`。包含 [CheckCXXSourceCompiles] 模块。然后，使用 `check_cxx_source_compiles` 确定 `log` 和 `exp` 是否可从 `cmath` 获得。如果它们可用，请使用 [target_compile_definitions()] 将 `HAVE_LOG` 和 `HAVE_EXP` 指定为编译定义。

在 `MathFunctions/mysqrt.cxx` 中，包含 `cmath`。然后，如果系统有 `log` 和 `exp`，用它们来计算平方根。

### 构建和运行

运行 [cmake] 可执行文件或 [cmake-gui] 来配置项目，然后使用您选择的构建工具构建它并运行 `Tutorial` 可执行文件。

这可能类似于以下内容：

``` shell
cmake -S . -B build
cmake --build build
```

`sqrt` 或 `mysqrt` 现在哪个函数给出更好的结果？

### 解决方案

在本练习中，我们将使用 [CheckCXXSourceCompiles] 模块中的函数，因此我们首先必须将其包含在 `MathFunctions/CMakeLists.txt` 中。

??? example "点击展开查看 `TODO 1`"

    ``` cmake title="MathFunctions/CMakeLists.txt"
    include(CheckCXXSourceCompiles)
    ```

然后使用 `check_cxx_compiles_source` 测试 `log` 和 `exp` 的可用性。这个函数让我们在真正的源代码编译之前尝试编译具有所需依赖关系的简单代码。结果变量 `HAVE_LOG` 和 `HAVE_EXP` 表示这些依赖项是否可用。

??? example "点击展开查看 `TODO 2`"

    ``` cmake title="MathFunctions/CMakeLists.txt"
    check_cxx_source_compiles("
        #include <cmath>
        int main() {
            std::log(1.0);
            return 0;
        }
        "
        HAVE_LOG
    )
    check_cxx_source_compiles("
        #include <cmath>
        int main() {
            std::exp(1.0);
            return 0;
        }
        "
        HAVE_EXP
    )
    ```

接下来，我们需要将这些 CMake 变量传递给我们的源代码。这样，我们的源代码就可以知道哪些资源可用。如果 `log` 和 `exp` 都可用，请使用 [target_compile_definitions()] 将 `HAVE_LOG` 和 `HAVE_EXP` 指定为 `PRIVATE` 编译定义。

??? example "点击展开查看 `TODO 3`"

    ``` cmake title="MathFunctions/CMakeLists.txt"
    if(HAVE_LOG AND HAVE_EXP)
        target_compile_definitions(MathFunctions
            PRIVATE
                "HAVE_LOG"
                "HAVE_EXP"
        )
    endif()
    ```

由于我们可能会使用 `log` 和 `exp`，因此我们需要修改 `mysqrt.cxx` 以包含 `cmath`。

??? example "点击展开查看 `TODO 4`"

    ``` cpp title="MathFunctions/mysqrt.cxx"
    #include <cmath>
    ```

如果系统上有 `log` 和 `exp`，则使用它们计算 `mysqrt` 函数中的平方根。 `MathFunctions/mysqrt.cxx` 中的 `mysqrt` 函数如下所示：

??? example "点击展开查看 `TODO 5`"

    ``` cpp title="MathFunctions/mysqrt.cxx"
    #if defined(HAVE_LOG) && defined(HAVE_EXP)
        double result = std::exp(std::log(x) * 0.5);
        std::cout << "Computing sqrt of " << x << " to be " << result << " using log and exp" << std::endl;
    #else
        double result = x;
    ```

[CheckCXXSourceCompiles]: https://cmake.org/cmake/help/latest/module/CheckCXXSourceCompiles.html#module:CheckCXXSourceCompiles
[target_compile_definitions()]: https://cmake.org/cmake/help/latest/command/target_compile_definitions.html#command:target_compile_definitions
[cmake]: https://cmake.org/cmake/help/latest/manual/cmake.1.html#manual:cmake(1)
[cmake-gui]: https://cmake.org/cmake/help/latest/manual/cmake-gui.1.html#manual:cmake-gui(1)
[ccmake]: https://cmake.org/cmake/help/latest/manual/ccmake.1.html#manual:ccmake(1)