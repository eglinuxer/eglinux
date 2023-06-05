---
title: 第 11 步 [添加导出配置]
description: 如何优雅地将软件分发给其他人？添加导出配置是关键。
serial: CMake 工程实践指南-基础教程
---

# 第 11 步 [添加导出配置]

在本教程的[安装和测试](./05_installing_and_testing.md)期间，我们为 CMake 添加了安装项目库和标头的功能。在[打包安装程序](./09_packaging_an_installer.md)期间，我们添加了打包此信息的功能，以便可以将其分发给其他人。

下一步是添加必要的信息，以便其他 CMake 项目可以使用我们的项目，无论是从构建目录、本地安装还是打包时。

第一步是更新我们的 [install(TARGETS)] 命令，不仅指定 `DESTINATION`，还指定 `EXPORT`。 `EXPORT` 关键字生成一个 CMake 文件，其中包含用于从安装树导入安装命令中列出的所有目标的代码。因此，让我们继续并通过更新 `MathFunctions/CMakeLists.txt` 中的安装命令来显式导出 `MathFunctions` 库，如下所示：

``` cmake title="MathFunctions/CMakeLists.txt"
set(installable_libs MathFunctions tutorial_compiler_flags)

if(TARGET SqrtLibrary)
    list(APPEND installable_libs SqrtLibrary)
endif()

install(
    TARGETS ${installable_libs}
    EXPORT MathFunctionsTargets
    DESTINATION lib
)

# install include headers
install(
    FILES MathFunctions.h
    DESTINATION include
)
```

现在我们已经导出了 `MathFunctions`，我们还需要显式安装生成的 `MathFunctionsTargets.cmake` 文件。这是通过将以下内容添加到顶级 `CMakeLists.txt` 的底部来完成的：

``` cmake title="CMakeLists.txt"
install(
    EXPORT MathFunctionsTargets
    FILE MathFunctionsTargets.cmake
    DESTINATION lib/cmake/MathFunctions
)
```

此时您应该尝试运行 CMake。如果一切设置正确，您将看到 CMake 将生成如下所示的错误：

``` shell
Target "MathFunctions" INTERFACE_INCLUDE_DIRECTORIES property contains
path:

  "******/Step11/MathFunctions"

which is prefixed in the source directory.
```

CMake 试图说明的是，在生成导出信息期间，它将导出一条与当前机器本质上相关的路径，并且在其他机器上无效。对此的解决方案是更新 `MathFunctions` [target_include_directories()] 以了解它在构建目录和`安装/打包`中使用时需要不同的 `INTERFACE` 位置。这意味着将 `MathFunctions` 的 [target_include_directories()] 调用转换为：

``` cmake title="MathFunctions/CMakeLists.txt"
target_include_directories(MathFunctions
    INTERFACE
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
)
```

更新后，我们可以重新运行 CMake 并验证它不再发出警告。

此时，我们已经让 CMake 正确打包了所需的目标信息，但我们仍需要生成一个 `MathFunctionsConfig.cmake`，以便 CMake [find_package()] 命令可以找到我们的项目。因此，让我们继续将新文件添加到名为 `Config.cmake.in` 的项目的顶层，其内容如下：

``` cmake title="Config.cmake.in"
@PACKAGE_INIT@

include ( "${CMAKE_CURRENT_LIST_DIR}/MathFunctionsTargets.cmake" )
```

然后，要正确配置和安装该文件，请将以下内容添加到顶层 `CMakeLists.txt` 的底部：

``` cmake title="CMakeLists.txt"
install(
    EXPORT MathFunctionsTargets
    FILE MathFunctionsTargets.cmake
    DESTINATION lib/cmake/MathFunctions
)

include(CMakePackageConfigHelpers)
```

接下来，我们执行 [configure_package_config_file()]。此命令将配置提供的文件，但与标准的 [configure_file()] 方式有一些具体差异。要正确使用此功能，除了所需的内容外，输入文件还应包含一行文本 `@PACKAGE_INIT@`。该变量将替换为将设置值转换为相对路径的代码块。这些新值可以用相同的名称引用，但要加上 `PACKAGE_` 前缀。

``` cmake title="CMakeLists.txt"
install(EXPORT MathFunctionsTargets
  FILE MathFunctionsTargets.cmake
  DESTINATION lib/cmake/MathFunctions
)

include(CMakePackageConfigHelpers)
# generate the config file that includes the exports
configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in
    "${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfig.cmake"
    INSTALL_DESTINATION "lib/cmake/example"
    NO_SET_AND_CHECK_MACRO
    NO_CHECK_REQUIRED_COMPONENTS_MACRO
)
```

接下来是 [write_basic_package_version_file()]。此命令写入一个由 [find_package()] 使用的文件，记录所需包的版本和兼容性。在这里，我们使用 `Tutorial_VERSION_*` 变量并表示它与 `AnyNewerVersion` 兼容，这表示此版本或任何更高版本都与请求的版本兼容。

``` cmake title="CMakeLists.txt"
write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfigVersion.cmake"
    VERSION "${Tutorial_VERSION_MAJOR}.${Tutorial_VERSION_MINOR}"
    COMPATIBILITY AnyNewerVersion
)
```

最后，设置要安装的两个生成的文件：

``` cmake title="CMakeLists.txt"
install(
    FILES
        ${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfig.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfigVersion.cmake
    DESTINATION
        lib/cmake/MathFunctions
)
```

至此，我们已经为我们的项目生成了一个可重定位的 CMake Configuration，可以在项目安装或打包后使用。如果我们希望我们的项目也可以从构建目录中使用，我们只需将以下内容添加到顶层 `CMakeLists.txt` 的底部：

``` cmake title="CMakeLists.txt"
export(EXPORT MathFunctionsTargets
    FILE "${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsTargets.cmake"
)
```

通过这个导出调用，我们现在生成一个 `MathFunctionsTargets.cmake`，允许其他项目使用构建目录中配置的 `MathFunctionsConfig.cmake`，而无需安装它。


[install(TARGETS)]: https://cmake.org/cmake/help/latest/command/install.html#command:install
[target_include_directories()]: https://cmake.org/cmake/help/latest/command/target_include_directories.html#command:target_include_directories
[find_package()]: https://cmake.org/cmake/help/latest/command/find_package.html#command:find_package
[configure_package_config_file()]: https://cmake.org/cmake/help/latest/module/CMakePackageConfigHelpers.html#command:configure_package_config_file
[configure_file()]: https://cmake.org/cmake/help/latest/command/configure_file.html#command:configure_file
[write_basic_package_version_file()]: https://cmake.org/cmake/help/latest/module/CMakePackageConfigHelpers.html#command:write_basic_package_version_file