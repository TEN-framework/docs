# 准备工作

TEN 框架支持 Windows、Linux 和 Mac 作为开发环境，但建议使用 Linux 或 Mac。

## 开发环境

### Linux

以 Ubuntu 为例，您必须首先安装以下软件包：

*   gcc：TEN 框架支持的 C/C++ 编译器之一。
*   clang：TEN 框架支持的另一种 C/C++ 编译器。
*   clang-format：TEN 框架在运行时使用的代码格式化工具。
*   clang-tidy：静态代码分析器。
*   cmake：构建系统。版本必须大于 3.13。
*   Python3：TEN 框架 Python 绑定所需。
*   pytest：用于 TEN 框架的集成测试。

### Mac

要设置您的 Mac 开发环境，您需要以下内容：

*   XCode
*   CocoaPods
*   个人账户

按照以下说明准备环境：

```shell
brew install llvm googletest doxygen ninja clang-format
brew install include-what-you-use
```

由于通过 `brew` 安装的 `cmake` 版本可能已过时，如果确实如此，您需要从官方网站安装 `cmake` 并设置符号链接。

```shell
ln -s /Applications/CMake.app/Contents/bin/cmake /usr/local/bin/cmake
```

如果 `cmake` 找不到 `clang-tidy`，您可以使用以下命令解决此问题：

```shell
ln -sf /usr/local/opt/llvm/bin/clang-tidy /usr/local/bin/clang-tidy
```

要构建 TEN 框架的各种语言绑定，您可以使用以下命令安装所需的语言：

```shell
brew install python golang
```

### Windows

如果您需要在 Microsoft Windows 上开发 TEN 框架，您可以手动安装以下软件包：

*   Visual Studio 社区版（最高至 2022）

    请务必选择与 clang 相关的工具并将 clang 二进制文件路径添加到 `PATH` 环境变量。

*   cmake
*   python

> ⚠️ **注意：**
> 在 Windows 上安装 `cmake` / `python` 时，安装路径不应包含空格。

## ten_gn

TEN 框架使用 `ten_gn` 作为其构建系统。`ten_gn` 是一个基于 Google GN 的构建系统。`ten_gn` 的源代码位于 TEN 框架存储库的 `core/ten_gn` 目录中。将 `ten_gn` 目录路径添加到开发环境中的系统 `PATH`。

## 使用 Docker 容器

我们提供预先编写的 Docker 文件，允许您创建一个容器，其中包含从源代码构建 TEN 框架所需的所有必要软件包。

### Ubuntu 22.04

导航到 `tools/docker_for_building/ubuntu/22.04` 并运行以下命令来创建并进入构建环境。

```shell
docker-compose up -d
docker-compose run ten-building-ubuntu-2204
```
