# TEN 元数据系统概述

TEN 框架在各种类型的包（包括：

*   应用程序
*   扩展组
*   扩展
*   协议
*   系统

## 元数据类型

在 TEN 元数据系统中，有两种主要类型的元数据：

### 1. **清单 (Manifest)**

*   **位置**：存储在关联的 TEN 包的根目录中，文件名为 `manifest.json`。
*   **内容**：
    *   **包名称**：TEN 包的名称。
    *   **包版本**：TEN 包的版本，遵循语义版本控制。
    *   **TEN 模式**：定义与包的属性和输入/输出消息相关的模式。
        *   **属性模式**：这些模式通常在根目录的 `property.json` 文件中定义。清单可以指定这些属性的模式。
        *   **消息模式**：定义包处理的输入/输出消息的模式。

> ⚠️ **注意**：`manifest.json` 中的 TEN 模式 *不是* JSON 模式。相反，它描述了 TEN 值的元数据，这些值是 TEN 运行时的核心，而 JSON 仅仅是一种表示格式。

### 2. **属性 (Property)**

*   **位置**：通常存储在 TEN 包根目录的 `property.json` 文件中。
*   **目的**：`property.json` 文件存储初始属性值，这些属性值在运行时是可读写的。这意味着可以在 TEN 运行时执行时修改属性。

#### `property.json` 示例

```json
{
  "Darth Vader": "I am your father"
}
```

## 清单文件 (Manifest File)

`manifest.json` 文件充当 TEN 包的蓝图，定义其元数据、属性以及它处理的输入/输出消息。

### `manifest.json` 示例

```json
{
  "type": "app",
  "name": "default_app_cpp",
  "version": "1.0.0",
  "dependencies": [
    {
      "type": "system",
      "name": "ten_runtime",
      "version": "1.0.0"
    }
  ],
  "api": {
    "property": {
      "exampleInt8": {
        "type": "int8"
      },
      "exampleString": {
        "type": "string"
      }
    },
    "cmd_in": [
      {
        "name": "cmd_foo",
        "property": {
          "foo": {
            "type": "int8"
          },
          "bar": {
            "type": "string"
          }
        },
        "result": {
          "property": {
            "detail": {
              "type": "string"
            },
            "aaa": {
              "type": "int8"
            },
            "bbb": {
              "type": "string"
            }
          }
        }
      }
    ],
    "cmd_out": [],
    "data_in": [
      {
        "name": "data_foo",
        "property": {
          "foo": {
            "type": "int8"
          },
          "bar": {
            "type": "string"
          }
        }
      }
    ],
    "data_out": [],
    "video_frame_in": [],
    "video_frame_out": [],
    "audio_frame_in": [],
    "audio_frame_out": []
  }
}
```

### 清单中 TEN 模式的目的

`manifest.json` 中的 TEN 模式为 TEN 运行时提供了有关扩展的外部 API 的元数据，包括属性和类型的类型信息。这有助于运行时在操作期间正确处理扩展的属性和消息。

### 何时使用 TEN 模式

1.  **属性验证**：当 TEN 运行时获取/设置 TEN 包或消息的属性时。
2.  **数据转换**：使用 `from_json` API 将 JSON 文档转换为 TEN 包或消息属性时。
3.  **兼容性检查**：当 TEN 管理器根据 TEN 模式检查消息是否可以从源扩展输出到目标扩展时。

## 属性管理

在 TEN 框架中，有两种类型的属性：

1.  **消息属性**：这些属性与框架中扩展之间交换的消息相关联。消息属性定义消息中携带的特定数据或参数，例如命令参数、数据负载或与音频/视频帧相关的元数据。

2.  **TEN 包属性**：这些属性与 TEN 包本身（例如扩展）相关联。TEN 包属性定义特定于包的配置或设置。例如，扩展可能具有配置其行为的属性，例如运行时设置、初始化参数或其他配置数据。

这两种类型的属性都在 TEN 框架中进行管理，但服务于不同的目的，一种侧重于组件之间的通信（消息属性），另一种侧重于组件本身的配置和操作（TEN 包属性）。

<figure><img src="../assets/png/property_system.png" alt=""><figcaption><p>属性系统</p></figcaption></figure>

### 定义 TEN 包属性

`property.json` 文件定义 TEN 包的属性。以下是一个示例：

```json
{
  "prop_1_name": 0,
  "prop_2_name": "prop_2_value",
  "prop_3_name": [
    "hello",
    "prop_3_sub_value"
  ],
  "prop_4_name": {
    "prop_4_1_name": 1,
    "prop_4_2_name": "prop_4_sub_value"
  }
}
```

> ⚠️ **注意**：`property.json` 文件中的每个属性名称必须是唯一的。

### 属性的 TEN 模式

您可以在 `manifest.json` 文件中为属性定义 TEN 模式，使 TEN 运行时能够更有效地处理这些属性。如果属性没有相应的 TEN 模式，则运行时将使用默认的 JSON 处理方法（例如，将所有 JSON 数字视为 `float64`）。如果提供了 TEN 模式，则运行时将使用它来验证和相应地处理属性。

| 属性 | TEN 模式 | 效果                                                                                                                                            |
| ---- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| 是   | 是       | TEN 运行时根据 TEN 模式验证属性值（例如，检查类型）。                                                                                                  |
| 是   | 否       | TEN 运行时使用默认处理，将所有 JSON 数字视为 `float64`。                                                                                              |

### 从扩展访问属性

TEN 运行时为扩展提供了访问各种属性的 API。

### 在 `start_graph` 命令中指定属性值

与 TEN 包相关的属性值可以在 `start_graph` 命令中指定。TEN 运行时根据 TEN 模式（如果已定义）处理这些属性，并将它们存储在相应的 TEN 包实例中。

#### `start_graph` 命令中属性规范的示例

```json
{
  "nodes": [{
    "type": "extension_group",
    "name": "foo_extension_group",
    "addon": "foo_extension_group_addon"
  },{
    "type": "extension",
    "name": "bar_extension",
    "extension_group": "foo_extension_group",
    "property": {
      "prop_1_name": 0,
      "prop_2_name": "prop_2_value",
      "prop_3_name": [
        "hello",
        "prop_3_sub_value"
      ],
      "prop_4_name": {
        "prop_4_1_name": 1,
        "prop_4_2_name": "prop_4_sub_value"
      }
    }
  }]
}
```
