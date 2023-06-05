---
title: 第 12 步 [打包调试和发布版本]
description: 不要光只会打包，还要分清楚调式版本和发布版本。
serial: CMake 工程实践指南-基础教程
---

# 第 12 步 [打包调试和发布版本]

!!! warning "警告"

    此示例对单配置生成器有效，不适用于多配置生成器（例如 `Visual Studio`）。

默认情况下，CMake 的模型是一个构建目录只包含一个配置，可以是 `Debug、Release、MinSizeRel` 或 `RelWithDebInfo`。 但是，可以将 CPack 设置为捆绑多个构建目录并构建一个包含同一项目的多个配置的包。

首先，我们要确保调试和发布版本对将要安装的库使用不同的名称。 让我们使用 `d` 作为调试库的后缀。

在顶级 `CMakeLists.txt` 文件的开头附近设置 [CMAKE_DEBUG_POSTFIX]：

``` cmake title="CMakeLists.txt"
set(CMAKE_DEBUG_POSTFIX d)

add_library(tutorial_compiler_flags INTERFACE)
```

以及教程可执行文件上的 [DEBUG_POSTFIX] 属性：

``` cmake title="CMakeLists.txt"
add_executable(Tutorial tutorial.cxx)
set_target_properties(Tutorial
    PROPERTIES
        DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX}
)

target_link_libraries(Tutorial
    PUBLIC
        MathFunctions
        tutorial_compiler_flags
)
```

我们还要向 `MathFunctions` 库添加版本编号。在 `MathFunctions/CMakeLists.txt` 中，设置 `VERSION` 和 `SOVERSION` 属性：

``` cmake title="MathFunctions/CMakeLists.txt"
set_property(
    TARGET
        MathFunctions
    PROPERTY
        VERSION "1.0.0"
)
set_property(
    TARGET
        MathFunctions
    PROPERTY
        SOVERSION "1"
)
```

在 `Step12` 目录中，创建 `debug` 和 `release` 子目录。布局将如下所示：

``` shell
- Step12
   - debug
   - release
```

现在我们需要设置调试和发布版本。我们可以使用 [CMAKE_BUILD_TYPE] 来设置配置类型：

``` shell
cd debug
cmake -DCMAKE_BUILD_TYPE=Debug ..
cmake --build .
cd ../release
cmake -DCMAKE_BUILD_TYPE=Release ..
cmake --build .
```

现在调试和发布版本都已完成，我们可以使用自定义配置文件将两个版本打包到一个版本中。在 `Step12` 目录中，创建一个名为 `MultiCPackConfig.cmake` 的文件。在此文件中，首先包含由 [cmake] 可执行文件创建的默认配置文件。

接下来，使用 `CPACK_INSTALL_CMAKE_PROJECTS` 变量指定要安装的项目。在这种情况下，我们要安装调试和发布。

``` cmake title="MultiCPackConfig.cmake"
include("release/CPackConfig.cmake")

set(CPACK_INSTALL_CMAKE_PROJECTS
    "debug;Tutorial;ALL;/"
    "release;Tutorial;ALL;/"
)
```

在 `Step12` 目录中，运行 [cpack] 并使用 `config` 选项指定我们的自定义配置文件：

``` shell
cpack --config MultiCPackConfig.cmake
```

[CMAKE_DEBUG_POSTFIX]: https://cmake.org/cmake/help/latest/variable/CMAKE_DEBUG_POSTFIX.html#variable:CMAKE_DEBUG_POSTFIX
[DEBUG_POSTFIX]: https://cmake.org/cmake/help/latest/prop_tgt/DEBUG_POSTFIX.html#prop_tgt:DEBUG_POSTFIX
[CMAKE_BUILD_TYPE]: https://cmake.org/cmake/help/latest/variable/CMAKE_BUILD_TYPE.html#variable:CMAKE_BUILD_TYPE
[cmake]: https://cmake.org/cmake/help/latest/manual/cmake.1.html#manual:cmake(1)
[cmake-gui]: https://cmake.org/cmake/help/latest/manual/cmake-gui.1.html#manual:cmake-gui(1)
[cpack]: https://cmake.org/cmake/help/latest/manual/cpack.1.html#manual:cpack(1)