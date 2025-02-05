# TEN管理器-设计器

要启动 `tman` 开发服务器，请使用以下命令：

{% code title=">_ 终端" %}

```shell
tman designer
```

{% endcode %}

如果未指定 `base-dir`，则默认使用当前工作目录。无论如何，`base-dir` 必须是 TEN 应用程序的基本目录。

服务器默认在端口 49483 上启动，您可以使用以下 URL 与设计器交互：

{% code title="https" %}

```text
http://127.0.0.1:49483/api/designer/v1/
```

{% endcode %}

如果找不到请求的端点 URL，客户端将收到 `404 Not Found` 响应，响应正文将包含 `Endpoint '\' not found` 以避免任何混淆。

## 版本

检索设计器的版本。

- **端点：** `/api/designer/v1/version`
- **动词：** GET

您将收到 `200 OK` 响应，正文包含如下 JSON 对象：

{% code title=".json" %}

```json
{
  "version": "0.1.0"
}
```

{% endcode %}

## 已安装的扩展插件

检索设计器在基本目录下识别的所有已安装的扩展插件。

- **端点：** `/api/designer/v1/addons/extensions`
- **动词：** GET

您将收到 `200 OK` 响应，正文包含如下 JSON 数组：

{% code title=".json" %}

```json
[
  {
    "name": "default_extension_cpp"
  },
  {
    "name": "simple_http_server_cpp"
  }
]
```

{% endcode %}

## 可用的图

检索可用图的列表。

- **端点：** `/api/designer/v1/graphs`
- **动词：** GET

您将收到 `200 OK` 响应，正文包含如下 JSON 数组：

{% code title=".json" %}

```json
[
  {
    "auto_start": true,
    "name": "0"
  }
]
```

{% endcode %}

如果发生错误，例如找不到应用程序包时，您将收到 `400 Bad Request` 响应，正文包含 `Failed to find any app packages`。

## 指定图中的扩展

检索指定图中的扩展列表。

- **端点：** `/api/designer/v1/graphs/{graph_id}/nodes`
- **动词：** GET

您将收到 `200 OK` 响应，正文包含 JSON 数组。例如：

{% code title=".json" %}

```json
{
  "status": "ok",
  "data": [
    {
      "addon": "addon_a",
      "name": "ext_a",
      "extension_group": "some_group",
      "app": "localhost",
      "api": {
        "property": {
          "foo": {
            "type": "string"
          }
        },
        "cmd_in": [
          {
            "name": "hello",
            "property": [
              {
                "name": "foo",
                "attributes": {
                  "type": "string"
                }
              }
            ],
            "result": {
              "property": [
                {
                  "name": "detail",
                  "attributes": {
                    "type": "string"
                  }
                }
              ]
            }
          }
        ],
        "data_out": [
          {
            "name": "hello",
            "property": [
              {
                "name": "bar",
                "attributes": {
                  "type": "Uint8"
                }
              }
            ]
          }
        ]
      },
      "property": {
        "foo": "1"
      }
    },
    {
      "addon": "addon_b",
      "name": "ext_b",
      "extension_group": "some_group",
      "app": "localhost",
      "api": {},
      "property": null
    }
  ]
}
```

{% endcode %}

`data` 数组的元素类型定义如下。

| 键              | 值类型 | 必需 | 描述                                              |
| :-------------- | :----: | :--: | :------------------------------------------------ |
| app             | string |  N  | 此扩展所属的应用程序的 URI，默认为“localhost”。 |
| extension_group | string |  Y  | 此扩展在其上运行的 extension_group。              |
| addon           | string |  Y  | 用于创建此扩展的插件。                            |
| name            | string |  Y  | 扩展名称。                                        |
| api             | object |  N  | 此扩展的属性和消息的架构定义。                    |
| property        | object |  N  | 此扩展的属性。                                    |

> 请注意，`data` 数组中的每个元素都由 `app`、`extension_group` 和 `name` 的组合唯一标识。

`api` 对象的定义。

| 键              | 值类型 | 必需 | 描述                              |
| :-------------- | :----: | :--: | :-------------------------------- |
| property        | object |  N  | 属性的架构。                      |
| cmd_in          | object |  N  | 所有 `IN` cmd 的架构。          |
| cmd_out         | object |  N  | 所有 `OUT` cmd 的架构。         |
| data_in         | object |  N  | 所有 `IN` data 的架构。         |
| data_out        | object |  N  | 所有 `OUT` data 的架构。        |
| audio_frame_in  | object |  N  | 所有 `IN` audio_frame 的架构。  |
| audio_frame_out | object |  N  | 所有 `OUT` audio_frame 的架构。 |
| video_frame_in  | object |  N  | 所有 `IN` video_frame 的架构。  |
| video_frame_out | object |  N  | 所有 `OUT` video_frame 的架构。 |

> 请注意，`cmd`、`data`、`audio_frame`、`video_frame` 是四种类型的 TEN msg。

`property` 的格式与架构定义相同。`data_in` / `data_out` / `audio_frame_in` / `audio_frame_out` / `video_frame_in` / `video_frame_out` 的格式相同，如下所示。

| 键                    | 值类型 | 必需 | 描述               |
| :-------------------- | :----: | :--: | :----------------- |
| name                  | string |  Y  | 消息名称。         |
| property              | array |  N  | 属于此消息的属性。 |
| property[].name       | string |  Y  | 属性名称。         |
| property[].attributes | object |  Y  | 此属性的架构定义。 |

`cmd_in` 和 `cmd_out` 的格式相同，并且与上面的 `data_in` 相比，还有一个额外的 `result` 属性。

| 键                    | 值类型 | 必需 | 描述                                              |
| :-------------------- | :----: | :--: | :------------------------------------------------ |
| name                  | string |  Y  | 消息名称。                                        |
| property              | array |  N  | 属于此消息的属性。                                |
| property[].name       | string |  Y  | 属性名称。                                        |
| property[].attributes | object |  Y  | 此属性的架构定义。                                |
| result                | object |  N  | 此 cmd 的相应结果的架构。                         |
| result.property       | array |  Y  | 属于此 cmd 结果的属性，格式与 `property` 相同。 |

## 指定图中的通信链路

检索指定图中的通信链路列表。

- **端点：** `/api/designer/v1/graphs/{graph_id}/connections`
- **动词：** GET

您将收到 `200 OK` 响应。例如：

{% code title=".json" %}

```json
{
  "status": "ok",
  "data": [
    {
      "app": "localhost",
      "extension": "ext_a",
      "cmd": [
        {
          "name": "cmd_1",
          "dest": [
            {
              "app": "localhost",
              "extension_group": "some_group",
              "extension": "ext_b",
              "msg_conversion": {
                "type": "per_property",
                "rules": [
                  {
                    "path": "extra_data",
                    "conversion_mode": "fixed_value",
                    "value": "tool_call"
                  }
                ],
                "keep_original": true
              }
            }
          ]
        }
      ]
    }
  ]
}
```

{% endcode %}

`data` 数组的元素类型定义如下。

| 键              | 值类型 | 必需 | 描述                                                                                         |
| :-------------- | :----: | :--: | :------------------------------------------------------------------------------------------- |
| app             | string |  N  | 与 `nodes` 中的 `app` 字段相同。                                                         |
| extension_group | string |  Y  | 与 `nodes` 中的 `extension_group` 字段相同。                                             |
| extension       | string |  Y  | 与 `nodes` 中的 **`name`** 字段相同。                                              |
| cmd             | array |  N  | 此扩展组将按类型发送的消息。可能的值为 `cmd`、`data`、`audio_frame`、`video_frame`。 |

`cmd` 数组的元素类型定义如下。

| 键                     | 值类型 | 必需 | 描述                                             |
| :--------------------- | :----: | :--: | :----------------------------------------------- |
| name                   | string |  Y  | 消息名称，在每个消息组中必须是唯一的。           |
| dest                   | array |  Y  | 此消息将发送到的扩展。                           |
| dest[].app             | string |  N  | 与 `nodes` 中的 `app` 字段相同。             |
| dest[].extension_group | string |  Y  | 与 `nodes` 中的 `extension_group` 字段相同。 |
| dest[].extension       | string |  Y  | 与 `nodes` 中的 `name` 字段相同。            |
| dest[].msg_conversion  | object |  N  | 用于在发送到目标之前转换消息的转换。             |

`msg_conversion` 的定义如下。

| 键                     | 值类型 | 必需 | 描述                                                          |
| :--------------------- | :-----: | :--: | :------------------------------------------------------------ |
| type                   | string |  Y  | 可能的值为 `per_property`。                                 |
| rules                  |  array  |  Y  | 转换规则。                                                    |
| rule[].path            | string |  Y  | 将应用此规则的属性的 JSON 路径。                              |
| rule[].conversion_mode | string |  Y  | 规则的方法，可能的值为 `fixed_value` 和 `from_original`。 |
| rule[].value           | string |  N  | 如果 conversion_mode 为 `fixed_value`，则为必需。           |
| rule[].original_path   | string |  N  | 如果 conversion_mode 为 `from_original`，则为必需。         |
| keep_original          | boolean |  N  | 是否克隆原始属性。默认为 false。                              |

## 检索所选扩展的兼容消息

从扩展中选择一条消息，并检索图中与该消息兼容的不同扩展中的所有其他消息。

- **端点：** `/api/designer/v1/messages/compatible`
- **动词：** POST

输入正文是一个 JSON 对象，表示请求查找指定图中特定扩展的输出命令的兼容引脚（通信链路）。

{% code title=".json" %}

```json
{
  "app": "localhost",
  "graph": "default",
  "extension_group": "extension_group_1",
  "extension": "extension_1",
  "msg_type": "cmd",
  "msg_direction": "out",
  "msg_name": "test_cmd"
}
```

{% endcode %}

您将收到 `200 OK` 响应，正文包含如下 JSON 数组：

{% code title=".json" %}

```json
[
  {
    "app": "localhost",
    "extension_group": "extension_group_1",
    "extension": "extension_2",
    "msg_type": "cmd",
    "msg_direction": "in",
    "msg_name": "test_cmd"
  },
  {
    "app": "localhost",
    "extension_group": "extension_group_1",
    "extension": "extension_3",
    "msg_type": "cmd",
    "msg_direction": "in",
    "msg_name": "test_cmd"
  }
]
```

{% endcode %}

## 更新图

更新指定的图。

- **端点：** `/api/designer/v1/graphs/{graph_id}`
- **动词：** PUT

输入数据（正文）：

{% code title=".json" %}

```json
{
  "auto_start": false,
  "nodes": [
    {
      "name": "extension_1",
      "addon": "extension_addon_1",
      "extension_group": "extension_group_1",
      "app": "localhost"
    },
    {
      "name": "extension_2",
      "addon": "extension_addon_2",
      "extension_group": "extension_group_1",
      "app": "localhost",
      "property": {
        "a": 1
      }
    }
  ],
  "connections": [
    {
      "app": "localhost",
      "extension": "extension_1",
      "cmd": [
        {
          "name": "hello_world",
          "dest": [
            {
              "app": "localhost",
              "extension": "extension_2"
            }
          ]
        }
      ]
    }
  ]
}
```

{% endcode %}

如果成功，客户端将收到 `200 OK` 响应；否则，将返回 `40x` 错误代码。

## 保存 `manifest.json`

保存 `manifest.json` 文件。

- **端点：** `/api/designer/v1/manifest`
- **动词：** PUT

如果成功，客户端将收到 `200 OK` 响应；否则，将返回 `40x` 错误代码。

## 保存 `property.json`

保存 `property.json` 文件，包括预定义的图和其他内容。

- **端点：** `/api/designer/v1/property`
- **动词：** PUT

如果成功，客户端将收到 `200 OK` 响应；否则，将返回 `40x` 错误代码。

## 如何开发设计器

**tman 设计器**包括前端和后端。虽然 tman 设计器的前端在编译期间直接嵌入到 tman 可执行文件中，但为了方便开发，tman 设计器的前端和后端可以单独运行和调试。

要启动 tman 设计器后端，请使用以下命令：

```shell
cargo run designer --base-dir <app-base-dir>
```

要独立启动 tman 设计器前端，请使用以下命令：

```shell
$ cd core/src/ten_manager/designer_frontend/

# 最好使用 bun，但也适用于 npm。
# 如果您没有 bun，请安装它。
# curl -fsSL https://bun.sh/install | bash
bun install
bun dev

# 或者使用 npm。
npm install
npm run dev
```

运行 `npm run start` 将启动一个 **webpack 开发服务器**来服务 tman 设计器前端。打开浏览器并导航到 `http://<ip>:3000` 以查看 tman 设计器前端。
