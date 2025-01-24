---
hidden: true
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 🚧 如何使用 C++ 构建扩展（测试版）

## 概述

本教程介绍了如何使用 C++ 开发 TEN 扩展，以及如何调试和部署它以在 TEN 应用程序中运行。本教程涵盖以下主题：

* 如何使用 tman 创建 C++ 扩展开发项目。
* 如何使用 TEN API 实现扩展的功能，例如发送和接收消息。
* 如何编写单元测试用例并调试代码。
* 如何在本地将扩展部署到应用程序中，并在应用程序内执行集成测试。
* 如何在应用程序内调试扩展代码。

注意

除非另有说明，否则本教程中的命令和代码在 Linux 环境中执行。由于 TEN 在所有平台（例如，Windows、Mac）上都具有一致的开发方法和逻辑，因此本教程也适用于其他平台。

### 准备工作

* 下载最新的 tman 并配置 PATH。您可以使用以下命令检查是否配置正确：

    ```
    $ tman -h
    ```

    如果配置成功，它将显示 tman 的帮助信息，如下所示：

    ```
    TEN manager

    用法：tman [选项] <命令>

    命令：
      install     安装一个软件包。有关更详细的用法，请运行“install -h”
      uninstall   卸载一个软件包。有关更详细的用法，请运行“uninstall -h”
      package     创建一个软件包文件。有关更详细的用法，请运行“package -h”
      publish     发布一个软件包。有关更详细的用法，请运行“publish -h”
      designer  安装一个软件包。有关更详细的用法，请运行“designer -h”
      help        打印此消息或给定子命令的帮助

    选项：
      -c, --config-file <CONFIG_FILE>  config.json 的位置
          --user-token <USER_TOKEN>    用户令牌
          --verbose                    启用详细输出
      -h, --help                       打印帮助
      -V, --version                    打印版本
    ```

* 下载最新的 ten\_gn 并配置 PATH。例如：

    注意

    ten\_gn 是 TEN 平台的 C++ 构建系统。为了方便开发人员，TEN 提供了一个 ten\_gn 工具链来构建 C++ 扩展项目。

    ```
    $ export PATH=/path/to/ten_gn:$PATH
    ```

    您可以使用以下命令检查是否配置成功：

    ```
    $ tgn -h
    ```

    用法：tgn [-h] [-v] [--verbose] [--out-dir OUT_DIR] command target-OS target-CPU build-type

    一个易于使用的 Google gn 包装器

    位置参数：
      command            可能的命令是：
                        gen         build        rebuild            refs    clean
                        graph       uninstall    explain_build      desc    check
                        show_deps   show_input   show_input_output  path    args
      target-OS          可能的 OS 值是：
                        win   mac   linux
      target-CPU         可能的值是：
                        x86   x64   arm   arm64
      build-type         可能的值是：
                        debug   release

    选项：
      -h, --help         显示此帮助消息并退出
      -v, --version      显示程序的版本号并退出
      --verbose          转储详细输出
      --out-dir OUT_DIR  构建输出目录，默认为“out/”

    我建议您将 /usr/local/ten_gn/.gnfiles 放入您的 PATH 中，以便您可以在任何地方运行 tgn。

    ```

    注意

  * gn 依赖于 python3，请确保已安装 Python 3.10 或更高版本。
* 安装 C/C++ 编译器，可以是 clang/clang++ 或 gcc/g++。

此外，我们提供了一个基本编译镜像，其中已安装和配置了上述所有依赖项。您可以参考 GitHub 上的 [TEN-Agent](https://github.com/ten-framework/TEN-Agent) 项目。

### 创建 C++ 扩展项目

#### 基于模板创建

假设我们要创建一个名为 first\_cxx\_extension 的项目，我们可以使用以下命令创建它：

```
$ tman install extension default_extension_cpp --template-mode --template-data package_name=first_cxx_extension
```

注意

以上命令表示我们正在使用 default\_extension\_cpp 模板安装 TEN 软件包，以创建名为 first\_cxx\_extension 的扩展项目。

*   \--template-mode 表示将 TEN 软件包作为模板安装。可以使用 --template-data 指定模板渲染参数。
*   extension 是要安装的 TEN 软件包的类型。目前，TEN 提供 app/extension\_group/extension/system 软件包。在以下关于在应用程序中测试扩展的部分中，我们将使用其他几种类型的软件包。
*   default\_extension\_cpp 是 TEN 提供的默认 C++ 扩展。开发人员还可以指定商店中可用的其他 C++ 扩展作为模板。

执行命令后，将在当前目录中生成一个名为 first\_cxx\_extension 的目录，这是我们的 C++ 扩展项目。目录结构如下：

```
.
├── BUILD.gn
├── manifest.json
├── property.json
└── src
  └── main.cc
```

其中：

*   src/main.cc 包含扩展的简单实现，包括对 TEN 提供的 C++ API 的调用。我们将在下一节讨论如何使用 TEN API。
*   manifest.json 和 property.json 是 TEN 扩展的标准配置文件。在 manifest.json 中，通常声明诸如扩展的版本、依赖项和架构定义之类的元数据信息。property.json 用于声明扩展的业务配置。
*   BUILD.gn 是 ten\_gn 的配置文件，用于编译 C++ 扩展项目。

property.json 文件最初是一个空的 JSON 文件，如下所示：

```
{}
```

manifest.json 文件默认将包括 ten\_runtime 依赖项，如下所示：

```
{
  "type": "extension",
  "name": "first_cxx_extension",
  "version": "0.2.0",
  "dependencies": [
  {
    "type": "system",
    "name": "ten_runtime",
    "version": "0.2.0"
  }
  ],
  "api": {}
}
```

注意

*   请注意，根据 TEN 的命名约定，名称应为字母数字。这是因为在将扩展集成到应用程序时，将根据扩展名称创建一个目录。TEN 还提供了从扩展目录自动加载 manifest.json 和 property.json 文件的功能。
*   依赖项用于声明扩展的依赖项。在安装 TEN 软件包时，tman 将根据依赖项部分中的声明自动下载依赖项。
*   api 部分用于声明扩展的架构。请参考 `ten 架构的用法 <usage_of_ten_schema_cn>`。

#### 手动创建

开发人员还可以手动创建 C++ 扩展项目或将现有项目转换为 TEN 扩展项目。

首先，确保项目的输出目标是共享库。然后，参考上面的示例在项目的根目录中创建 `property.json` 和 `manifest.json`。`manifest.json` 应包括 `type`、`name`、`version`、`language` 和 `dependencies` 等信息。具体而言：

*   `type` 必须为 `extension`。
*   `language` 必须为 `cpp`。
*   `dependencies` 应包括 `ten_runtime`。

最后，配置构建设置。TEN 提供的 `default_extension_cpp` 使用 `ten_gn` 作为构建工具链。如果开发人员使用不同的构建工具链，他们可以参考 `BUILD.gn` 中的配置来设置编译参数。由于 `BUILD.gn` 包含 TEN 软件包的目录结构，我们将在下一节（下载依赖项）中讨论它。

### 下载依赖项

要下载依赖项，请在扩展项目目录中执行以下命令：

```
$ tman install
```

成功执行命令后，将在当前目录中生成一个 `.ten` 目录，其中包含当前扩展的所有依赖项。

注意

*   扩展有两种模式：开发模式和运行时模式。在开发模式下，根目录是扩展的源代码目录。在运行时模式下，根目录是应用程序目录。因此，依赖项的放置路径在这两种模式下是不同的。这里提到的 `.ten` 目录是开发模式下依赖项的根目录。

目录结构如下：

```
.
├── BUILD.gn
├── manifest.json
├── property.json
├── .ten
│   └── app
│       ├── addon
│       ├── include
│       └── lib
└── src
  └── main.cc
```

其中：

*   `.ten/app/include` 是头文件的根目录。
*   `.ten/app/lib` 是 TEN 运行时预编译动态库的根目录。

如果在运行时模式下，扩展将放置在应用程序的 `addon/extension` 目录中，动态库将放置在应用程序的 `lib` 目录中。结构如下：

```
.
├── BUILD.gn
├── manifest.json
├── property.json
├── addon
│   └── extension
│       └── first_cxx_extension
├── include
└── lib
```

到目前为止，已经创建了一个 TEN C++ 扩展项目。

### BUILD.gn

`default_extension_cpp` 的 `BUILD.gn` 内容如下：

```
import("//exts/ten/base_options.gni")
import("//exts/ten/ten_package.gni")

config("common_config") {
  defines = common_defines
  include_dirs = common_includes
  cflags = common_cflags
  cflags_c = common_cflags_c
  cflags_cc = common_cflags_cc
  cflags_objc = common_cflags_objc
  cflags_objcc = common_cflags_objcc
  libs = common_libs
  lib_dirs = common_lib_dirs
  ldflags = common_ldflags
}

config("build_config") {
  configs = [ ":common_config" ]

  # 1. `include` 指的是当前扩展中的 `include` 目录。
  # 2. `//include` 指的是运行 `tgn gen` 的基本目录中的 `include` 目录。
  # 3. `.ten/app/include` 用于扩展独立构建。
  include_dirs = [
  "include",
  "//core/include",
  "//include/nlohmann_json",
  ".ten/app/include",
  ".ten/app/include/nlohmann_json",
  ]

  lib_dirs = [
  "lib",
  "//lib",
  ".ten/app/lib",
  ]

  if (is_win) {
  libs = [
    "ten_runtime.dll.lib",
    "utils.dll.lib",
  ]
  } else {
  libs = [
    "ten_runtime",
    "utils",
  ]
  }
}

ten_package("first_cxx_extension") {
  package_type = "develop"  # develop | release
  package_kind = "extension"

  manifest = "manifest.json"
  property = "property.json"

  if (package_type == "develop") {
  # 它是“develop”软件包，因此需要构建结果。
  build_type = "shared_library"

  sources = [ "src/main.cc" ]

  configs = [ ":build_config" ]
  }
}
```

我们首先看一下 `ten_package` 目标，它声明了 TEN 软件包的构建目标。

*   `package_kind` 设置为 `extension`，`build_type` 设置为 `shared_library`。这意味着编译的预期输出是一个共享库。
*   `sources` 字段指定要编译的源文件。如果有多个源文件，则需要将它们添加到 `sources` 字段中。
*   `configs` 字段指定构建配置。它引用此文件中定义的 `build_config`。

接下来，让我们看一下 `build_config` 的内容。

*   `include_dirs` 字段定义了头文件的搜索路径。
    *   `include` 和 `//include` 之间的区别在于，`include` 指的是当前扩展目录中的 `include` 目录，而 `//include` 基于 `tgn gen` 命令的工作目录。因此，如果在扩展目录中执行编译，它将与 `include` 相同。但如果在应用程序目录中执行，它将是应用程序中的 `include` 目录。
    *   `.ten/app/include` 用于扩展的独立开发和编译，这是本教程中讨论的场景。换句话说，默认的 `build_config` 与开发模式和运行时模式编译兼容。
*   `lib_dirs` 字段定义了依赖库的搜索路径。`lib` 和 `//lib` 之间的区别与 `include` 类似。
*   `libs` 字段定义了依赖库。`ten_runtime` 和 `utils` 是 TEN 提供的库。

因此，如果开发人员使用不同的构建工具链，他们可以参考以上配置并在自己的构建工具链中设置编译参数。例如，如果使用 g++ 进行编译：

```
$ g++ -shared -fPIC -I.ten/app/include/ -L.ten/app/lib -lten_runtime -lutils -Wl,-rpath=\$ORIGIN -Wl,-rpath=\$ORIGIN/../../../lib src/main.cc
```

`rpath` 的设置也考虑了运行时模式，其中扩展的 ten\_runtime 依赖项放置在 `app/lib` 目录中。

### 扩展功能的实现

对于开发人员来说，需要做两件事：

*   创建扩展作为与 TEN 运行时交互的通道。
*   在 TEN 中将扩展注册为插件，允许通过声明方式在图中使用它。

#### 创建扩展类

开发人员创建的扩展需要继承 `ten::extension_t` 类。该类的主要定义如下：

```cpp
class extension_t {
protected:
  explicit extension_t(const char *name) {...}

  virtual void on_init(ten_t &ten, metadata_info_t &manifest,
                     metadata_info_t &property) {
    ten.on_init_done(manifest, property);
  }

  virtual void on_start(ten_t &ten) { ten.on_start_done(); }

  virtual void on_stop(ten_t &ten) { ten.on_stop_done(); }

  virtual void on_deinit(ten_t &ten) { ten.on_deinit_done(); }

  virtual void on_cmd(ten_t &ten, std::unique_ptr<cmd_t> cmd) {
    auto cmd_result = ten::cmd_result_t::create(TEN_STATUS_CODE_OK);
    cmd_result->set_property("detail", "default");
    ten.return_result(std::move(cmd_result), std::move(cmd));
  }

  virtual void on_data(ten_t &ten, std::unique_ptr<data_t> data) {}

  virtual void on_pcm_frame(ten_t &ten, std::unique_ptr<pcm_frame_t> frame) {}

  virtual void on_image_frame(ten_t &ten,
                              std::unique_ptr<image_frame_t> frame) {}
}
```

在您提供的 markdown 内容中，有对生命周期函数和中文消息处理函数的描述。以下是翻译：

生命周期函数：

*   on\_init：用于初始化扩展实例，例如设置扩展的配置。
*   on\_start：用于启动扩展实例，例如建立与外部服务的连接。在 on\_start 完成之前，扩展不会接收消息。在 on\_start 中，您可以使用 ten.get\_property API 来检索扩展的配置。
*   on\_stop：用于停止扩展实例，例如关闭与外部服务的连接。
*   on\_deinit：用于销毁扩展实例，例如释放内存资源。

消息处理函数：

*   on\_cmd/on\_data/on\_pcm\_frame/on\_image\_frame：这些是用于接收四种不同类型消息的回调方法。有关 TEN 消息类型的更多信息，您可以参考[消息系统](https://github.com/TEN-framework/ten_framework/blob/main/docs/ten_framework/message_system.md)

ten::extension\_t 类为这些函数提供了默认实现，开发人员可以根据需要重写它们。

#### 注册扩展

定义扩展后，需要将其注册为 TEN 运行时中的插件。例如，在 `first_cxx_extension/src/main.cc` 文件中，注册代码如下：

```cpp
TEN_CPP_REGISTER_ADDON_AS_EXTENSION(first_cxx_extension, first_cxx_extension_extension_t);
```

*   TEN\_CPP\_REGISTER\_ADDON\_AS\_EXTENSION 是 TEN 运行时提供的用于注册扩展插件的宏。
    *   第一个参数是插件的名称，它作为插件的唯一标识符。它将用于通过声明方式在图中定义扩展。
    *   第二个参数是扩展的实现类，它是继承自 ten::extension\_t 的类。

请注意，插件名称必须是唯一的，因为它用作在图中查找实现的唯一索引。

#### on\_init

开发人员可以在 on\_init() 函数中设置扩展的配置，如示例所示：

```cpp
void on_init(ten::ten_t& ten, ten::metadata_info_t& manifest,
             ten::metadata_info_t& property) override {
  property.set(TEN_METADATA_JSON_FILENAME, "customized_property.json");
  ten.on_init_done(manifest, property);
}
```

可以使用 set() 方法自定义属性和清单。在示例中，第一个参数 TEN\_METADATA\_JSON\_FILENAME 表示自定义属性存储为本地文件，第二个参数是相对于扩展目录的文件路径。因此，在此示例中，当应用程序加载扩展时，它将加载 `<app>/addon/extension/first_cxx_extension/customized_property.json`。

TEN 的 on\_init 提供了加载默认配置的默认逻辑。如果开发人员不调用 property.set()，则默认情况下将加载扩展目录中的 property.json 文件。类似地，如果不调用 manifest.set()，则默认情况下将加载扩展目录中的 manifest.json 文件。在示例中，由于调用了 property.set()，因此默认情况下不会加载 property.json 文件。

请注意，on\_init 是一个异步方法，开发人员需要调用 ten.on\_init\_done() 以告知 TEN 运行时 on\_init 已按预期完成。

#### on\_start

当调用 on\_start 时，表示已执行 on\_init\_done() 并且已加载扩展的属性。从此时起，扩展可以访问配置。例如：

```cpp
void on_start(ten::ten_t& ten) override {
  auto prop = ten.get_property_string("some_string");
  // do something

  ten.on_start_done();
}
```

ten.get\_property\_string() 用于检索名为“some\_string”的字符串类型属性。如果该属性不存在或类型不匹配，则将返回错误。如果扩展的配置包含以下内容：

```json
{
  "some_string": "hello world"
}
```

则 prop 的值将为“hello world”。

与 on\_init 类似，on\_start 也是一个异步方法，开发人员需要调用 ten.on\_start\_done() 以告知 TEN 运行时 on\_start 已按预期完成。

有关更多信息，您可以参考 API 文档：ten api doc。

#### 错误处理

如前面的示例所示，如果 “some\_string” 不存在或不是字符串类型，则 ten.get\_property\_string() 将返回错误。您可以按如下方式处理错误：

```C++
void on_start(ten::ten_t& ten) override {
  ten::error_t err;
  auto prop = ten.get_property_string("some_string", &err);

  // 错误处理
  if (!err.is_success()) {
    TEN_LOGE("Failed to get property: %s", err.errmsg());
  }

  ten.on_start_done();
}
```
#### 消息处理

TEN 提供四种类型的消息：`cmd`、`data`、`image_frame` 和 `pcm_frame`。开发人员可以通过实现 `on_cmd`、`on_data`、`on_image_frame` 和 `on_pcm_frame` 回调方法来处理这四种类型的消息。

以 `cmd` 为例，让我们看看如何接收和发送消息。

假设 `first_cxx_extension` 接收到一个名为 `hello` 的 `cmd`，其中包括以下属性：

| 名称             | 类型   |
| ---------------- | ------ |
| app\_id          | string |
| client\_type     | int8   |
| payload          | object |
| payload.err\_no  | uint8  |
| payload.err\_msg | string |

`first_cxx_extension` 对 `hello` cmd 的处理逻辑如下：

*   如果 `app_id` 或 `client_type` 参数无效，则返回错误：

    ```json
    {
      "err_no": 1001,
      "err_msg": "Invalid argument."
    }
    ```

*   如果 `payload.err_no` 大于 0，则返回包含 `payload` 内容的错误。
*   如果 `payload.err_no` 等于 0，则将 `hello` cmd 转发到下游以进行进一步处理。在收到下游扩展的处理结果后，返回结果。

**在 manifest.json 中描述扩展的行为**

基于以上描述，`first_cxx_extension` 的行为如下：

*   它接收一个名为 `hello` 的 `cmd`，其中包含属性。
*   它可能会发送一个名为 `hello` 的 `cmd`，其中包含属性。
*   它接收来自下游扩展的响应，其中包括错误信息。
*   它向上游扩展返回一个响应，其中包括错误信息。

对于 TEN 扩展，您可以在扩展的 `manifest.json` 文件中描述上述行为，包括：

*   扩展接收哪些消息，它们的名称以及其属性的结构定义（架构定义）。
*   扩展生成/发送哪些消息，它们的名称以及其属性的结构定义。
*   此外，对于 `cmd` 类型消息，需要响应定义（在 TEN 中称为结果）。

通过这些定义，TEN 运行时将在将消息传递到扩展之前或在扩展通过 TEN 运行时发送消息时，基于架构定义执行有效性检查。它还有助于扩展的用户查看协议定义。

架构在 `manifest.json` 文件的 `api` 字段中定义。`cmd_in` 定义扩展将接收的 cmd，而 `cmd_out` 定义扩展将发送的 cmd。

注意

有关架构的用法，请参阅：[TEN 框架架构系统](https://github.com/TEN-framework/ten_framework/blob/main/docs/ten_framework/schema_system.md)

基于以上描述，`first_cxx_extension` 的 `manifest.json` 内容如下：

```json
{
  "type": "extension",
  "name": "first_cxx_extension",
  "version": "0.2.0",
  "dependencies": [
    {
      "type": "system",
      "name": "ten_runtime",
      "version": "0.2.0"
    }
  ],
  "api": {
    "cmd_in": [
      {
        "name": "hello",
        "property": {
          "app_id": {
            "type": "string"
          },
          "client_type": {
            "type": "int8"
          },
          "payload": {
            "type": "object",
            "properties": {
              "err_no": {
                "type": "uint8"
              },
              "err_msg": {
                "type": "string"
              }
            }
          }
        },
        "required": ["app_id", "client_type"],
        "result": {
          "property": {
            "err_no": {
              "type": "uint8"
            },
            "err_msg": {
              "type": "string"
            }
          },
          "required": ["err_no"]
        }
      }
    ],
    "cmd_out": [
      {
        "name": "hello",
        "property": {
          "app_id": {
            "type": "string"
          },
          "client_type": {
            "type": "string"
          },
          "payload": {
            "type": "object",
            "properties": {
              "err_no": {
                "type": "uint8"
              },
              "err_msg": {
                "type": "string"
              }
            }
          }
        },
        "required": ["app_id", "client_type"],
        "result": {
          "property": {
            "err_no": {
              "type": "uint8"
            },
            "err_msg": {
              "type": "string"
            }
          },
          "required": ["err_no"]
        }
      }
    ]
  }
}
```

**获取请求数据**

在 `on_cmd` 方法中，第一步是检索请求数据，即 cmd 中的属性。我们定义一个 `request_t` 类来表示请求数据。

在扩展项目的 `include` 目录中创建一个名为 `model.h` 的文件，内容如下：

```cpp
#pragma once

#include "nlohmann/json.hpp"
#include <cstdint>
#include <string>

namespace first_cxx_extension_extension {

class request_payload_t {
public:
  friend void from_json(const nlohmann::json &j, request_payload_t &payload);

  friend class request_t;

private:
  uint8_t err_no;
  std::string err_msg;
};

class request_t {
public:
  friend void from_json(const nlohmann::json &j, request_t &request);

private:
  std::string app_id;
  int8_t client_type;
  request_payload_t payload;
};

} // namespace first_cxx_extension_extension
```

在 `src` 目录中，创建一个名为 `model.cc` 的文件，内容如下：

```cpp
#include "model.h"

namespace first_cxx_extension_extension {
void from_json(const nlohmann::json &j, request_payload_t &payload) {
  if (j.contains("err_no")) {
    j.at("err_no").get_to(payload.err_no);
  }

  if (j.contains("err_msg")) {
    j.at("err_msg").get_to(payload.err_msg);
  }
}

void from_json(const nlohmann::json &j, request_t &request) {
  if (j.contains("app_id")) {
    j.at("app_id").get_to(request.app_id);
  }

  if (j.contains("client_type")) {
    j.at("client_type").get_to(request.client_type);
  }

  if (j.contains("payload")) {
    j.at("payload").get_to(request.payload);
  }
}
} // namespace first_cxx_extension_extension
```

要解析请求数据，您可以使用 TEN 提供的 `get_property` API。以下是如何实现的示例：

```cpp
// model.h

class request_t {
public:
  void from_cmd(ten::cmd_t &cmd);

  // ...
}

// model.cc

void request_t::from_cmd(ten::cmd_t &cmd) {
  app_id = cmd.get_property_string("app_id");
  client_type = cmd.get_property_int8("client_type");

  auto payload_str = cmd.get_property_to_json("payload");
  if (!payload_str.empty()) {
    auto payload_json = nlohmann::json::parse(payload_str);
    from_json(payload_json, payload);
  }
}
```

要返回响应，您需要创建一个 `cmd_result_t` 对象并相应地设置属性。然后，将 `cmd_result_t` 对象传递给 TEN 运行时以返回给请求者。以下是一个示例：

```cpp
// model.h

class request_t {
public:
  bool validate(std::string *err_msg) {
    if (app_id.length() < 64) {
      *err_msg = "invalid app_id";
      return false;
    }

    return true;
  }
}

// main.cc

void on_cmd(ten::ten_t &ten, std::unique_ptr<ten::cmd_t> cmd) override {
  request_t request;
  request.from_cmd(*cmd);

  std::string err_msg;
  if (!request.validate(&err_msg)) {
    auto result = ten::cmd_result_t::create(TEN_STATUS_CODE_ERROR);
    result->set_property("err_no", 1);
    result->set_property("err_msg", err_msg.c_str());

    ten.return_result(std::move(result), std::move(cmd));
  }
}
```

在上面的示例中，`ten::cmd_result_t::create` 用于创建具有错误代码的 `cmd_result_t` 对象。`result.set_property` 用于设置 `cmd_result_t` 对象的属性。最后，调用 `ten.return_result` 以将 `cmd_result_t` 对象返回给请求者。

**将请求传递给下游扩展**

如果扩展需要向另一个扩展发送消息，则可以调用 `send_cmd()` API。以下是一个示例：

```cpp
void on_cmd(ten::ten_t &ten, std::unique_ptr<ten::cmd_t> cmd) override {
  request_t request;
  request.from_cmd(*cmd);

  std::string err_msg;
  if (!request.validate(&err_msg)) {
    // ...
  } else {
    ten.send_cmd(std::move(cmd));
  }
}
```

`send_cmd()` 中的第一个参数是请求的命令，第二个参数是返回的 `cmd_result_t` 的处理程序。第二个参数也可以省略，表示不需要对返回的结果进行特殊处理。如果该命令最初是从更高级别的扩展发送的，则运行时将自动将其返回到更高级别的扩展。

开发人员还可以传递一个响应处理程序，如下所示：

```cpp
ten.send_cmd(
    std::move(cmd),
    [](ten::ten_t &ten, std::unique_ptr<ten::cmd_result_t> result) {
      ten.return_result_directly(std::move(result));
    });
```

在上面的示例中，响应处理程序中使用了 `return_result_directly()` 方法。您可以看到此方法与 `return_result()` 的不同之处在于它不传递原始命令对象。这主要是因为：

*   对于 TEN 消息对象（cmd/data/pcm\_frame/image\_frame），所有权在消息回调方法（例如 `on_cmd()`）中转移到扩展。这意味着一旦扩展收到命令，TEN 运行时将不会对其执行任何读/写操作。当扩展调用 `send_cmd()` 或 `return_result()` API 时，这意味着扩展正在将命令的所有权返回给 TEN 运行时以进行进一步处理，例如消息传递。此后，扩展不应再对该命令执行任何读/写操作。
*   响应处理程序中的 `result`（即 `send_cmd()` 的第二个参数）由下游扩展返回，此时，该结果已绑定到命令，这意味着运行时具有该结果的返回路径信息。因此，无需再次传递命令对象。

当然，开发人员也可以在响应处理程序中处理结果。

到目前为止，一个简单命令处理逻辑的示例已经完成。对于其他消息类型（例如数据），您可以参考 TEN API 文档。

### 在本地部署到应用程序以进行集成测试

tman 提供了发布到本地注册表的功能，允许您在本地执行集成测试，而无需将扩展上传到中央存储库。与 GO 扩展不同，对于 C++ 扩展，对应用程序的编程语言没有严格的要求。它可以是 GO、C++ 或 Python。

不同应用程序的部署过程可能会有所不同。具体步骤如下：

*   设置 tman 本地注册表。
*   将扩展上传到本地注册表。
*   从中央存储库下载应用程序（default\_app\_cpp/default\_app\_go）以进行集成测试。
*   对于 C++ 应用程序：
    *   在应用程序目录中安装 first\_cxx\_extension。
    *   在应用程序目录中编译。此时，应用程序和扩展都将被编译到 out/linux/x64/app/default\_app\_cpp 目录中。
    *   在 out/linux/x64/app/default\_app\_cpp 中安装所需的依赖项。测试的工作目录是当前目录。
*   对于 GO 应用程序：
    *   在应用程序目录中安装 first\_cxx\_extension。
    *   在 addon/extension/first\_cxx\_extension 目录中编译，因为 GO 和 C++ 编译工具链不同。
    *   在应用程序目录中安装依赖项。测试的工作目录是应用程序目录。
*   在应用程序的 manifest.json 中配置图，指定消息的接收者为 first\_cxx\_extension，并发送测试消息。

#### 将扩展上传到本地注册表

首先，创建一个临时 config.json 文件以设置 tman 本地注册表。例如，/tmp/code/config.json 的内容如下：

```json
{
  "registry": {
    "default": {
      "index": "file:///tmp/code/repository"
    }
  }
}
```

这将本地目录 `/tmp/code/repository` 设置为 tman 本地注册表。

注意

*   小心不要将其放置在 \~/.tman/config.json 中，因为它会影响后续从中央存储库下载依赖项。

然后，在 first\_cxx\_extension 目录中，执行以下命令将扩展上传到本地注册表：

```
$ tman --config-file /tmp/code/config.json publish
```

命令完成后，可以在 /tmp/code/repository/extension/first\_cxx\_extension/0.1.0 目录中找到上传的扩展。

#### 准备用于测试的应用程序（C++）

1.  在空目录中安装 default\_app\_cpp 作为测试应用程序。

    > ```
    > $ tman install app default_app_cpp
    > ```
    >
    > 成功执行命令后，当前目录中将有一个名为 default\_app\_cpp 的目录。
    >
    > 注意
    >
    > *   安装应用程序时，将自动安装其依赖项。

2.  在应用程序目录中安装我们想要测试的 first\_cxx\_extension。

    > 执行以下命令：
    >
    > ```
    > $ tman --config-file /tmp/code/config.json install extension first_cxx_extension
    > ```
    >
    > 完成命令后，addon/extension 目录中将有一个 first\_cxx\_extension 目录。
    >
    > 注意
    >
    > *   重要的是要注意，由于 first\_cxx\_extension 在本地注册表中，因此具有本地注册表指定的 --config-file 的配置文件路径需要与发布时相同。

3.  添加一个扩展作为消息生产者。

    > `first_cxx_extension` 预期接收 `hello` cmd，因此我们需要一个消息生产者。一种方法是添加一个扩展作为消息生产者。为了方便生成测试消息，可以将 http 服务器集成到生产者的扩展中。
    >
    > 首先，基于 `default_extension_cpp` 创建一个 http 服务器扩展。在应用程序目录中执行以下命令：
    >
    > ```
    > $ tman install extension default_extension_cpp --template-mode --template-data package_name=http_server
    > ```
    >
    > http 服务器的主要功能是：
    >
    > *   在扩展的 `on_start()` 中启动一个运行 http 服务器的线程。
    > *   将传入的请求转换为名为 `hello` 的 TEN cmd，并使用 `send_cmd()` 发送它们。
    > *   期望接收 `cmd_result_t` 响应，并将其内容写入 http 响应。
    >
    > 在这里，我们使用 cpp-httplib ([https://github.com/yhirose/cpp-httplib](https://github.com/yhirose/cpp-httplib)) 作为 http 服务器的实现。
    >
    > 首先，下载 httplib.h 并将其放置在扩展的 include 目录中。然后，在 src/main.cc 中添加 http 服务器的实现。以下是一个示例代码：
    >
    > ```cpp
    > #include "httplib.h"
    > #include "nlohmann/json.hpp"
    > #include "ten_runtime/binding/cpp/ten.h"
    >
    > namespace http_server_extension {
    >
    > class http_server_extension_t : public ten::extension_t {
    > public:
    >   explicit http_server_extension_t(const char *name)
    >       : extension_t(name) {}
    >
    >   void on_start(ten::ten_t &ten) override {
    >     ten_proxy = ten::ten_proxy_t::create(ten);
    >     srv_thread = std::thread([this] {
    >       server.Get("/health",
    >                 [](const httplib::Request &req, httplib::Response &res) {
    >                   res.set_content("OK", "text/plain");
    >                 });
    >
    >       // Post handler, receive json body.
    >       server.Post("/hello", [this](const httplib::Request &req,
    >                                   httplib::Response &res) {
    >         // Receive json body.
    >         auto body = nlohmann::json::parse(req.body);
    >         body["ten"]["name"] = "hello";
    >
    >         auto cmd = ten::cmd_t::create_from_json(body.dump().c_str());
    >         auto cmd_shared =
    >             std::make_shared<std::unique_ptr<ten::cmd_t>>(std::move(cmd));
    >
    >         std::condition_variable *cv = new std::condition_variable();
    >
    >         auto response_body = std::make_shared<std::string>();
    >
    >         ten_proxy->notify([cmd_shared, response_body, cv](ten::ten_t &ten) {
    >           ten.send_cmd(
    >               std::move(*cmd_shared),
    >               [response_body, cv](ten::ten_t &ten,
    >                                   std::unique_ptr<ten::cmd_result_t> result) {
    >                 auto err_no = result->get_property_uint8("err_no");
    >                 if (err_no > 0) {
    >                   auto err_msg = result->get_property_string("err_msg");
    >                   response_body->append(err_msg);
    >                 } else {
    >                   response_body->append("OK");
    >                 }
    >
    >                 cv->notify_one();
    >               });
    >         });
    >
    >         std::unique_lock<std::mutex> lk(mtx);
    >         cv->wait(lk);
    >         delete cv;
    >
    >         res.set_content(response_body->c_str(), "text/plain");
    >       });
    >
    >       server.listen("0.0.0.0", 8001);
    >     });
    >
    >     ten.on_start_done();
    >   }
    >
    >   void on_stop(ten::ten_t &ten) override {
    >     // Extension stop.
    >
    >     server.stop();
    >     srv_thread.join();
    >     delete ten_proxy;
    >
    >     ten.on_stop_done();
    >   }
    >
    > private:
    >   httplib::Server server;
    >   std::thread srv_thread;
    >   ten::ten_proxy_t *ten_proxy{nullptr};
    >   std::mutex mtx;
    > };
    >
    > TEN_CPP_REGISTER_ADDON_AS_EXTENSION(http_server, http_server_extension_t);
    >
    > } // namespace http_server_extension
    > ```
    >
    > 这里，在 `on_start()` 中创建一个新线程来运行 http 服务器，因为我们不想阻止扩展线程。这样，转换后的 cmd 请求将由 `srv_thread` 生成和发送。在 TEN 运行时中，为了确保线程安全，我们使用 `ten_proxy_t` 来传递来自扩展线程之外的线程的 `send_cmd()` 之类的调用。
    >
    > 此代码还演示了如何在 `on_stop()` 中清理外部资源。对于扩展，您应该在 `on_stop_done()` 之前释放 `ten_proxy_t`，这将停止外部线程。

4.  配置图。

    在应用程序的 `manifest.json` 中，配置 `predefined_graph` 以指定由 `http_server` 生成的 `hello` cmd 应发送到 `first_cxx_extension`。例如：

    > ```json
    > "predefined_graphs": [
    >   {
    >     "name": "testing",
    >     "auto_start": true,
    >     "nodes": [
    >       {
    >         "type": "extension_group",
    >         "name": "http_thread",
    >         "addon": "default_extension_group"
    >       },
    >       {
    >         "type": "extension",
    >         "name": "http_server",
    >         "addon": "http_server",
    >         "extension_group": "http_thread"
    >       },
    >       {
    >         "type": "extension",
    >         "name": "first_cxx_extension",
    >         "addon": "first_cxx_extension",
    >         "extension_group": "http_thread"
    >       }
    >     ],
    >     "connections": [
    >       {
    >         "extension": "http_server",
    >         "cmd": [
    >           {
    >             "name": "hello",
    >             "dest": [
    >               {
    >                 "extension": "first_cxx_extension"
    >               }
    >             ]
    >           }
    >         ]
    >       }
    >     ]
    >   }
    > ]
    > ```

5.  编译应用程序。

    > 在应用程序目录中执行以下命令：
    >
    > ```
    > $ tgn gen linux x64 debug
    > $ tgn build linux x64 debug
    > ```
    >
    > 编译完成后，应用程序和扩展的编译输出将生成在目录 out/linux/x64/app/default\_app\_cpp 中。
    >
    > 但是，此时无法直接运行它，因为它缺少扩展组的依赖项。

6.  安装扩展组。

    > 切换到编译输出目录。
    >
    > ```
    > $ cd out/linux/x64/app/default_app_cpp
    > ```
    >
    > 安装扩展组。
    >
    > ```
    > $ tman install extension_group default_extension_group
    > ```

7.  启动应用程序。

    > 在编译输出目录中，执行以下命令：
    >
    > ```
    > $ ./bin/default_app_cpp
    > ```
    >
    > 应用程序启动后，您现在可以通过向 http 服务器发送消息来测试它。例如，使用 curl 发送一个带有无效 app\_id 的请求：
    >
    > ```
    > $ curl --location 'http://127.0.0.1:8001/hello' \
    >   --header 'Content-Type: application/json' \
    >   --data '{
    >       "app_id": "123",
    >       "client_type": 1,
    >       "payload": {
    >           "err_no": 0
    >       }
    >   }'
    > ```
    >
    > 预期的响应应该是“invalid app\_id”。

### 在应用程序中调试扩展

#### 应用程序（C++）

C++ 应用程序被编译成一个可执行文件，并设置了正确的 `rpath`。因此，调试 C++ 应用程序只需要在 `.vscode/launch.json` 中添加以下配置：

```json
"configurations": [
  {
      "name": "App (C/C++) (lldb, launch)",
      "type": "lldb",
      "request": "launch",
      "program": "${workspaceFolder}/out/linux/x64/app/default_app_cpp/bin/default_app_cpp",
      "args": [],
      "cwd": "${workspaceFolder}/out/linux/x64/app/default_app_cpp"
  }
  ]
```
