---
title: 第 9 步 [打包安装程序]
description: 做好打包是每个软件开发者都应该具备的能力。
serial: CMake 工程实践指南-基础教程
---

# 第 9 步 [打包安装程序]

接下来假设我们想要将我们的项目分发给其他人，以便他们可以使用它。我们希望在各种平台上提供二进制和源代码分发。这与我们之前在安装和测试中所做的安装略有不同，我们在[安装和测试](./05_installing_and_testing.md)中安装了我们从源代码构建的二进制文件。

在此示例中，我们将构建支持二进制安装和包管理功能的安装包。为此，我们将使用 CPack 创建特定于平台的安装程序。具体来说，我们需要在顶级 `CMakeLists.txt` 文件的底部添加几行。

``` cmake title="CMakeLists.txt"
include(InstallRequiredSystemLibraries)
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
set(CPACK_PACKAGE_VERSION_MAJOR "${Tutorial_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${Tutorial_VERSION_MINOR}")
set(CPACK_SOURCE_GENERATOR "TGZ")
include(CPack)
```

这就是它的全部。我们首先包括 [InstallRequiredSystemLibraries]。该模块将包含当前平台项目所需的任何运行时库。接下来，我们将一些 CPack 变量设置为我们存储该项目的许可证和版本信息的位置。

版本信息已在本教程前面设置，`License.txt` 已包含在该步骤的顶级源目录中。 [CPACK_SOURCE_GENERATOR] 变量选择源包的文件格式。

最后，我们包括 [CPack] 模块，它将使用这些变量和当前系统的一些其他属性来设置安装程序。

下一步是以通常的方式构建项目，然后运行 ​[​cpack] 可执行文件。要构建二进制分发版，请从二进制目录运行：

``` shell
cpack
```

要指定生成器，请使用 `-G` 选项。对于多配置构建，使用 `-C` 指定配置。例如：

``` shell
cpack -G ZIP -C Debug
```

有关可用生成器的列表，请参阅 [cpack-generators(7)] 或调用 `cpack --help`。像 `ZIP` 这样的[存档生成器]会创建所有已安装文件的压缩存档。

要创建完整源代码树的存档，命令如下：

``` shell
cpack --config CPackSourceConfig.cmake
```

或者，运行 `make package` 或并从 `IDE` 中构建项目右键单击 `Package` 目标。

运行在二进制目录中找到的安装程序。然后运行安装的可执行文件并验证它是否有效。


[InstallRequiredSystemLibraries]: https://cmake.org/cmake/help/latest/module/InstallRequiredSystemLibraries.html#module:InstallRequiredSystemLibraries
[CPACK_SOURCE_GENERATOR]: https://cmake.org/cmake/help/latest/module/CPack.html#variable:CPACK_SOURCE_GENERATOR
[CPack]: https://cmake.org/cmake/help/latest/module/CPack.html#module:CPack
​[​cpack]: https://cmake.org/cmake/help/latest/manual/cpack.1.html#manual:cpack(1)
[cpack-generators(7)]: https://cmake.org/cmake/help/latest/manual/cpack-generators.7.html#manual:cpack-generators(7)
[存档生成器]: https://cmake.org/cmake/help/latest/cpack_gen/archive.html#cpack_gen:CPack%20Archive%20Generator