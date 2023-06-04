---
hide:
  - toc
---

# CMake 基础教程

## 介绍

CMake 教程提供了一个分步指南，涵盖了 CMake 帮助解决的常见构建系统问题。了解各种主题如何在一个示例项目中一起工作会很有帮助。

## 步骤

本教程的源码可以从 [eglinux/study_cmake/tutorial] 获取，也可以从 CMake 官方下载：[官方源码压缩包]。每个步骤都有自己的子目录，其中包含可用作起点的代码。教程示例是渐进的，因此每个步骤都为上一步提供完整的解决方案。

- [第 1 步 一个简单的起点](./01_a_basic_starting_point.md)
    - [练习 1 - 构建一个基础项目](./01_a_basic_starting_point.md#练习-1---构建一个基本项目)
    - [练习 2 - 指定 C++ 标准](./01_a_basic_starting_point.md#练习-2---指定-c-标准)
    - [练习 3 - 添加版本号和配置头文件](./01_a_basic_starting_point.md#练习-3---添加版本号和配置头文件)
- [第 2 步 添加一个库](./02_adding_a_library.md)
    - [练习1 - 创建一个库](./02_adding_a_library.md#练习1---创建一个库)
    - [练习2 - 将库设置可选](./02_adding_a_library.md#练习2---将库设置可选)
- [第 3 步 为库添加使用要求](./03_adding_usage_requirements_for_a_library.md)
    - [练习 1 - 为库添加使用要求](./03_adding_usage_requirements_for_a_library.md#练习-1---为库添加使用要求)
- [第 4 步 添加生成表达式](./04_adding_eenerator_expressions.md)
    - [练习 1 - 使用接口库设定 C++ 标准](./04_adding_eenerator_expressions.md#练习-1---使用接口库设定-c-标准)
    - [练习 2 - 使用生成器表达式添加编译器警告标志](./04_adding_eenerator_expressions.md#练习-2---使用生成器表达式添加编译器警告标志)
- [第 5 步 安装和测试](./05_installing_and_testing.md)
    - [练习 1 - 安装规则](./05_installing_and_testing.md#练习-1---安装规则)
    - [练习 2 - 测试支持](./05_installing_and_testing.md#练习-2---测试支持)
- [第 6 步 添加测试看板支持](./06_adding_support_for_a_testing_dashboard.md)
    - [练习 1 - 将结果发送到测试看板](./06_adding_support_for_a_testing_dashboard.md#练习-1---将结果发送到测试看板)
- [第 7 步 添加系统分析](./07_adding_system_introspection.md)
    - [练习 1 - 评估依赖项可用性](./07_adding_system_introspection.md#练习-1---评估依赖项可用性)
- [第 8 步 自定义命令和生成文件](./08_adding_a_custom_gommand_and_generated_file.md)
- [第 9 步 打包安装程序](./09_packaging_an_installer.md)
- [第 10 步 选择静态库或共享库](./10_selecting_static_or_shared_libraries.md)
- [第 11 步 添加导出配置](./11_adding_export_configuration.md)
- [第 12 步 打包调试和发布版本](./12_packaging_debug_and_release.md)



[eglinux/study_cmake/tutorial]: https://github.com/eglinuxer/study_cmake/tree/main/tutorial
[官方源码压缩包]: https://cmake.org/cmake/help/latest/_downloads/987664e19bf1c78e58910f17f64df29f/cmake-3.26.4-tutorial-source.zip