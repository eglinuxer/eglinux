---
title: 第 6 步 [添加测试看板支持]
description: 添加对将我们的测试结果提交到看板的支持很简单
serial: CMake 工程实践指南-基础教程
---

# 第 6 步 [添加测试看板支持]

添加对将我们的测试结果提交到看板的支持很简单。我们已经在[测试支持](./05_installing_and_testing.md#2-)中为我们的项目定义了许多测试。现在我们只需运行这些测试并将它们提交给 CDash。

## 练习 1 - 将结果发送到测试看板

### 要完成的目标

使用 CDash 显示我们的 CTest 结果。

### 参考资源

- [ctest(1)]
- [include()]
- [CTest]

### 需要编辑的文件

- `CMakeLists.txt`

### 入门

对于本练习，通过包含 CTest 模块来完成顶层 `CMakeLists.txt` 中的 `TODO 1`。这将启用使用 CTest 进行测试以及向 CDash 提交看板，因此我们可以安全地删除对 [enable_testing()] 的调用。

我们还需要获取一个 `CTestConfig.cmake` 文件以放置在顶级目录中。运行时，[ctest] 可执行文件将读取此文件以收集有关测试仪表板的信息。它包含了：

- 项目名称
- 项目“每晚“的开始时间
    - 此项目的 24 小时“一天”开始的时间。
- 提交生成的文档将发送到的 CDash 实例的 URL

对于本教程，使用了一个公共仪表板服务器，并在该步骤的根目录中为您提供了相应的 `CTestConfig.cmake` 文件。实际上，该文件将从旨在托管测试结果的 CDash 实例上的项目设置页面下载。从 CDash 下载后，不应在本地修改该文件。

``` cmake title="CTestConfig.cmake"
set(CTEST_PROJECT_NAME "CMakeTutorial")
set(CTEST_NIGHTLY_START_TIME "00:00:00 EST")

set(CTEST_DROP_METHOD "http")
set(CTEST_DROP_SITE "my.cdash.org")
set(CTEST_DROP_LOCATION "/submit.php?project=CMakeTutorial")
set(CTEST_DROP_SITE_CDASH TRUE)
```

### 构建和运行

请注意，作为 CDash 提交的一部分，有关您的开发系统的一些信息（例如站点名称或完整路径名）可能会公开显示。

要创建一个简单的测试仪表板，请运行 [cmake] 可执行文件或 [cmake-gui] 来配置项目，但先不要构建它。相反，导航到构建目录并运行：

``` shell
ctest [-VV] -D Experimental
```

请记住，对于多配置生成器（例如 `Visual Studio`），必须指定配置类型：

``` shell
ctest [-VV] -C Debug -D Experimental
```

或者，从 `IDE` 构建 `Experimental` 目标。

[ctest] 可执行文件将构建项目、运行任何测试并将结果提交到 `Kitware` 的[公共仪表板](https://my.cdash.org/index.php?project=CMakeTutorial)。

### 解决方案

此步骤中唯一需要更改的 CMake 代码是通过在我们的顶级 `CMakeLists.txt` 中包含 CTest 模块来启用向 CDash 的仪表板提交：

??? example "点击展开查看 `TODO 1`"

    ``` cmake
    include(CTest)
    ```

[ctest(1)]: https://cmake.org/cmake/help/latest/manual/ctest.1.html#manual:ctest(1)
[cmake]: https://cmake.org/cmake/help/latest/manual/cmake.1.html#manual:cmake(1)
[cmake-gui]: https://cmake.org/cmake/help/latest/manual/cmake-gui.1.html#manual:cmake-gui(1)
[ccmake]: https://cmake.org/cmake/help/latest/manual/ccmake.1.html#manual:ccmake(1)
[include()]: https://cmake.org/cmake/help/latest/command/include.html#command:include
[CTest]: https://cmake.org/cmake/help/latest/module/CTest.html#module:CTest
[enable_testing()]: https://cmake.org/cmake/help/latest/command/enable_testing.html#command:enable_testing