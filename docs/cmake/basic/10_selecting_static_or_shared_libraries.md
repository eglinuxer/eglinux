# 第 10 步 [选择静态库或共享库]

在本节中，我们将展示如何使用 [BUILD_SHARED_LIBS] 变量来控制 [add_library()] 的默认行为，并允许控制如何构建没有显式类型（`STATIC`、`SHARED`、`MODULE` 或 `OBJECT`）的库。

为此，我们需要将 [BUILD_SHARED_LIBS] 添加到顶层 `CMakeLists.txt`。我们使用 [option()] 命令，因为它允许用户有选择地选择值应该是 `ON` 还是 `OFF`。

接下来我们要重构 `MathFunctions` 成为一个真正的库，使用 `mysqrt` 或 `sqrt` 进行封装，而不是要求调用代码来做这个逻辑。这也意味着 `USE_MYMATH` 不会控制 `MathFunctions` 的构建，而是会控制这个库的行为。

第一步是将顶层 `CMakeLists.txt` 的起始部分更新为如下所示：

``` cmake title="CMakeLists.txt"
cmake_minimum_required(VERSION 3.15)

# set the project name and version
project(Tutorial VERSION 1.0)

# specify the C++ standard
add_library(tutorial_compiler_flags INTERFACE)
target_compile_features(tutorial_compiler_flags
    INTERFACE
        cxx_std_11
)

# add compiler warning flags just when building this project via the BUILD_INTERFACE genex
set(gcc_like_cxx "$<COMPILE_LANG_AND_ID:CXX,ARMClang,AppleClang,Clang,GNU,LCC>")
set(msvc_cxx "$<COMPILE_LANG_AND_ID:CXX,MSVC>")

target_compile_options(tutorial_compiler_flags
    INTERFACE
        "$<${gcc_like_cxx}:$<BUILD_INTERFACE:-Wall;-Wextra;-Wshadow;-Wformat=2;-Wunused>>"
        "$<${msvc_cxx}:$<BUILD_INTERFACE:-W3>>"
)

# control where the static and shared libraries are built so that on windows
# we don't need to tinker with the path to run the executable
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}")

option(BUILD_SHARED_LIBS "Build using shared libraries" ON)

# configure a header file to pass the version number only
configure_file(TutorialConfig.h.in TutorialConfig.h)

# add the MathFunctions library
add_subdirectory(MathFunctions)

# add the executable
add_executable(Tutorial tutorial.cxx)
target_link_libraries(Tutorial
    PUBLIC
        MathFunctions
        tutorial_compiler_flags
)
```

现在我们已经使 `MathFunctions` 始终可用，我们将需要更新该库的逻辑。因此，在 `MathFunctions/CMakeLists.txt` 中，我们需要创建一个 `SqrtLibrary`，它将在启用 `USE_MYMATH` 时有条件地构建和安装。现在，由于这是一个教程，我们将明确要求 `SqrtLibrary` 是静态构建的。

最终结果是 `MathFunctions/CMakeLists.txt` 应该如下所示：

``` cmake title="MathFunctions/CMakeLists.txt"
# add the library that runs
add_library(MathFunctions MathFunctions.cxx)

# state that anybody linking to us needs to include the current source dir
# to find MathFunctions.h, while we don't.
target_include_directories(MathFunctions
    INTERFACE
        ${CMAKE_CURRENT_SOURCE_DIR}
)

# should we use our own math functions
option(USE_MYMATH "Use tutorial provided math implementation" ON)
if(USE_MYMATH)
    target_compile_definitions(MathFunctions
        PRIVATE
            "USE_MYMATH"
    )

    # first we add the executable that generates the table
    add_executable(MakeTable MakeTable.cxx)
    target_link_libraries(MakeTable
        PRIVATE
            tutorial_compiler_flags
    )

    # add the command to generate the source code
    add_custom_command(
        OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/Table.h
        COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/Table.h
        DEPENDS MakeTable
    )

    # library that just does sqrt
    add_library(SqrtLibrary
        STATIC
            mysqrt.cxx
            ${CMAKE_CURRENT_BINARY_DIR}/Table.h
    )

    # state that we depend on our binary dir to find Table.h
    target_include_directories(SqrtLibrary
        PRIVATE
            ${CMAKE_CURRENT_BINARY_DIR}
    )

    target_link_libraries(SqrtLibrary
        PUBLIC
            tutorial_compiler_flags
    )
    target_link_libraries(MathFunctions
        PRIVATE
            SqrtLibrary
    )
endif()

target_link_libraries(MathFunctions
    PUBLIC
        tutorial_compiler_flags
)

# define the symbol stating we are using the declspec(dllexport) when
# building on windows
target_compile_definitions(MathFunctions
    PRIVATE
        "EXPORTING_MYMATH"
)

# install libs
set(installable_libs MathFunctions tutorial_compiler_flags)

if(TARGET SqrtLibrary)
    list(APPEND installable_libs SqrtLibrary)
endif()

install(
    TARGETS ${installable_libs}
    DESTINATION lib
)

# install include headers
install(
    FILES MathFunctions.h
    DESTINATION include
)
```

接下来，更新 `MathFunctions/mysqrt.cxx` 以使用 `mathfunctions` 和 `detail` 命名空间：

``` cpp title="MathFunctions/mysqrt.cxx"
#include <iostream>

#include "MathFunctions.h"

// include the generated table
#include "Table.h"

namespace mathfunctions {
namespace detail {
// a hack square root calculation using simple operations
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

}
}
```

我们还需要对 `tutorial.cxx` 进行一些更改，使其不再使用 `USE_MYMATH`：

1. 始终包含 `MathFunctions.h`
2. 始终使用 `mathfunctions::sqrt`
3. 不包含 `cmath`

最后，更新 `MathFunctions/MathFunctions.h` 以使用 `dll` 导出定义：

``` cpp title="MathFunctions/MathFunctions.h"
#if defined(_WIN32)
    #if defined(EXPORTING_MYMATH)
        #define DECLSPEC __declspec(dllexport)
    #else
        #define DECLSPEC __declspec(dllimport)
    #endif
#else // non windows
    #define DECLSPEC
#endif

namespace mathfunctions {
    double DECLSPEC sqrt(double x);
}
```

此时，如果您构建所有内容，您可能会注意到链接失败，因为我们将一个没有位置无关代码的静态库与一个具有位置无关代码的库组合在一起。解决方案是在构建共享库时将 `SqrtLibrary` 的 [POSITION_INDEPENDENT_CODE] 目标属性显式设置为 `True`。

``` cmake title="MathFunctions/CMakeLists.txt"
# state that SqrtLibrary need PIC when the default is shared libraries
set_target_properties(SqrtLibrary
    PROPERTIES
        POSITION_INDEPENDENT_CODE ${BUILD_SHARED_LIBS}
)
```

练习：我们修改了 `MathFunctions.h` 以使用 `dll` 导出定义。使用 CMake 文档，你能找到一个帮助模块来简化这个吗？

[BUILD_SHARED_LIBS]: https://cmake.org/cmake/help/latest/variable/BUILD_SHARED_LIBS.html#variable:BUILD_SHARED_LIBS
[add_library()]: https://cmake.org/cmake/help/latest/command/add_library.html#command:add_library
[option()]: https://cmake.org/cmake/help/latest/command/option.html#command:option
[POSITION_INDEPENDENT_CODE]: https://cmake.org/cmake/help/latest/prop_tgt/POSITION_INDEPENDENT_CODE.html#prop_tgt:POSITION_INDEPENDENT_CODE