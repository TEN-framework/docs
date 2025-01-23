# TEN Manager - 检查图

`tman` 提供了 `check graph` 命令来验证预定义的图或 `start_graph` 命令的正确性。要查看使用详情，请使用以下命令：

```shell
$ tman check graph -h
检查预定义图或 start_graph 命令的图内容是否正确。有关更详细的用法，请运行“graph -h”

用法：tman check graph [选项] --app <APP>

选项：
      --app <APP>
          图中定义的应用程序的绝对路径。默认情况下，将从列表中的第一个读取预定义的图。
      --predefined-graph-name <PREDEFINED_GRAPH_NAME>
          指定要检查的预定义图名称，否则将检查所有预定义的图。
      --graph <GRAPH>
          指定要检查的“start_graph”命令的 JSON 字符串。如果未指定，将检查第一个应用程序中的预定义图。
  -h, --help
          打印帮助
```

`check graph` 命令旨在处理可能跨越多个应用程序的图，因此不需要从特定应用程序的根目录运行。相反，它可以从任何目录执行，并使用 `--app` 参数指定图中涉及的应用程序的文件夹。

## 用法示例

-   **检查 `property.json` 中的所有预定义图**：

    ```shell
    tman check graph --app /home/TEN-Agent/agents
    ```

-   **检查特定的预定义图**：

    ```shell
    tman check graph --predefined-graph-name va.openai.azure --app /home/TEN-Agent/agents
    ```

-   **检查 `start_graph` 命令**：

    ```shell
    tman check graph --graph '{
      "type": "start_graph",
      "seq_id": "55",
      "nodes": [
        {
          "type": "extension",
          "name": "test_extension",
          "addon": "basic_hello_world_2__test_extension",
          "extension_group": "test_extension_group",
          "app": "msgpack://127.0.0.1:8001/"
        },
        {
          "type": "extension",
          "name": "test_extension",
          "addon": "basic_hello_world_1__test_extension",
          "extension_group": "test_extension_group",
          "app": "msgpack://127.0.0.1:8001/"
        }
      ]
    }' --app /home/TEN-Agent/agents
    ```

## 前提条件

-   **预定义图定义要求**：如果指定了预定义图名称，则将从第一个 `--app` 参数指定的应用程序的 `property.json` 文件中提取定义。

-   **软件包安装要求**：在运行 `check graph` 命令之前，必须使用 `tman install` 命令安装应用程序依赖的所有扩展。这是必要的，因为验证过程需要有关图中每个扩展的信息，例如其 `manifest.json` 文件中定义的 API。

-   **唯一的应用程序 URI 要求**：在多应用程序图中，每个应用程序的 `property.json` 必须定义一个唯一的 `_ten::uri`。此外，`uri` 值不能设置为 `"localhost"`。

## 验证规则

### 1. 节点的存在

任何图定义中都需要 `nodes` 数组。如果不存在，将引发错误。

-   **示例（未定义节点）**：

    ```shell
    tman check graph --graph '{
      "type": "start_graph",
      "seq_id": "55",
      "nodes": []
    }' --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    正在检查图[0]... ❌。详情：
      图中未定义任何扩展节点。

    全部完成。
    ❌  错误：1/1 个图失败。
    ```

### 2. 节点的唯一性

`nodes` 数组中的每个节点都表示应用程序组中特定扩展实例，由指定的插件创建。因此，每个扩展实例都应由单个节点唯一表示。节点必须通过 `app`、`extension_group` 和 `name` 的组合来唯一标识。不允许同一扩展实例的多个条目。

-   **示例（重复节点）**：

    ```shell
    tman check graph --graph '{
      "type": "start_graph",
      "seq_id": "55",
      "nodes": [
        {
          "type": "extension",
          "name": "test_extension",
          "addon": "basic_hello_world_2__test_extension",
          "extension_group": "test_extension_group",
          "app": "msgpack://127.0.0.1:8001/"
        },
        {
          "type": "extension",
          "name": "test_extension",
          "addon": "basic_hello_world_1__test_extension",
          "extension_group": "test_extension_group",
          "app": "msgpack://127.0.0.1:8001/"
        }
      ]
    }' --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    正在检查图[0]... ❌。详情：
      在 nodes[1] 中发现重复扩展，插件：basic_hello_world_1__test_extension，名称：test_extension。

    全部完成。
    ❌  错误：1/1 个图失败。
    ```

### 3. 连接中使用的扩展应在节点中定义

`connections` 字段中引用的所有扩展实例（无论是作为源还是目标）都必须在 `nodes` 字段中显式定义。任何未在 `nodes` 数组中定义的实例都会导致验证错误。

-   **示例（未定义源扩展）**：

    假设 TEN 应用程序的 `property.json` 的内容如下。

    ```json
    {
      "_ten": {
        "predefined_graphs": [
          {
            "name": "default",
            "auto_start": false,
            "nodes": [
              {
                "type": "extension",
                "name": "some_extension",
                "addon": "default_extension_go",
                "extension_group": "some_group"
              }
            ],
            "connections": [
              {
                "extension": "some_extension",
                "cmd": [
                  {
                    "name": "hello",
                    "dest": [
                      {
                        "extension": "some_extension"
                      }
                    ]
                  }
                ]
              }
            ]
          }
        ]
      }
    }
    ```

    ```shell
    tman check graph --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    正在检查图[0]... ❌。详情：
      connections[0] 中声明的扩展未在节点中定义，扩展组：producer，扩展：some_extension。

    全部完成。
    ❌  错误：1/1 个图失败。
    ```

-   **示例（未定义目标扩展）**：

    假设 TEN 应用程序的 `property.json` 的内容如下。

    ```json
    {
      "_ten": {
        "predefined_graphs": [
          {
            "name": "default",
            "auto_start": false,
            "nodes": [
              {
                "type": "extension",
                "name": "some_extension",
                "addon": "default_extension_go",
                "extension_group": "some_group"
              },
              {
                "type": "extension",
                "name": "some_extension_1",
                "addon": "default_extension_go",
                "extension_group": "some_group"
              }
            ],
            "connections": [
              {
                "extension": "some_extension",
                "cmd": [
                  {
                    "name": "hello",
                    "dest": [
                      {
                        "extension": "some_extension_1"
                      }
                    ]
                  },
                   {
                     "name": "world",
                     "dest": [
                       {
                         "extension": "some_extension_1"
                       },
                       {
                         "extension": "consumer"
                       }
                     ]
                   }
                ]
              }
            ]
          }
        ]
      }
    }
    ```

    ```shell
    tman check graph --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    正在检查图[0]... ❌。详情：
      connections[0].cmd[1] 中声明的扩展未在节点中定义，扩展组：some_group，扩展：consumer。

    全部完成。
    ❌  错误：1/1 个图失败。
    ```

### 4. 节点中声明的插件必须安装在应用程序中

-   **示例（`property.json` 中的 `_ten::uri` 不等于节点中的 `app` 字段）**：

    假设 TEN 应用程序的 property.json 的内容如下。

    ```json
    {
      "_ten": {
        "predefined_graphs": [
          {
            "name": "default",
            "auto_start": false,
            "nodes": [
              {
                "type": "extension",
                "name": "some_extension",
                "addon": "default_extension_go",
                "extension_group": "some_group"
              }
            ]
          }
        ],
        "uri": "http://localhost:8001"
      }
    }
    ```

    ```shell
    tman check graph --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    正在检查图[0]... ❌。详情：
      节点中声明但未安装以下软件包：[("localhost", Extension, "default_extension_go")]。

    全部完成。
    ❌  错误：1/1 个图失败。
    ```

    问题是应用程序中的所有软件包都将存储在以应用程序的 `uri` 为键的映射中，并且图中的每个节点都通过 `app` 字段（默认为 `localhost`）检索。节点中的 `app`（即 localhost）与应用程序的 `uri`（即 <http://localhost:8001>）不匹配。

-   **示例（由于尚未执行 `tman install`，因此 `ten_packages` 不存在）**：

    假设 TEN 应用程序的 `property.json` 的内容如下。

    ```json
    {
      "_ten": {
        "predefined_graphs": [
          {
            "name": "default",
            "auto_start": false,
            "nodes": [
              {
                "type": "extension",
                "name": "some_extension",
                "addon": "default_extension_go",
                "extension_group": "some_group"
              }
            ]
          }
        ]
      }
    }
    ```

    并且 `ten_packages` 目录**不**存在。

    ```shell
    tman check graph --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    正在检查图[0]... ❌。详情：
      节点中声明但未安装以下软件包：[("localhost", Extension, "default_extension_go")]。

    全部完成。
    ❌  错误：1/1 个图失败。
    ```

### 5. 在连接中，从一个扩展发送的消息应在同一部分中定义

-   **示例**：

    假设已安装所有软件包。

    ```shell
    tman check graph --graph '{
      "nodes": [
        {
          "type": "extension",
          "name": "some_extension",
          "addon": "default_extension_go",
          "extension_group": "some_group"
        },
        {
          "type": "extension",
          "name": "another_ext",
          "addon": "default_extension_go",
          "extension_group": "some_group"
        }
      ],
      "connections": [
        {
          "extension": "some_extension",
          "cmd": [
            {
              "name": "hello",
              "dest": [
                {
                  "extension": "another_ext"
                }
              ]
            }
          ]
        },
        {
          "extension": "some_extension",
          "cmd": [
            {
              "name": "hello_2",
              "dest": [
                {
                  "extension": "another_ext"
                }
              ]
            }
          ]
        }
      ]
    }' --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    正在检查图[0]... ❌。详情：
      扩展“some_extension”在 connection[0] 和 connection[1] 中定义，请将它们合并为一个部分。

    全部完成。
    ❌  错误：1/1 个图失败。
    ```

### 6. 在连接中，从一个扩展发送的消息在每种类型中应具有唯一的名称

-   **示例**：

    假设已安装所有软件包。

    ```shell
    tman check graph --graph '{
      "nodes": [
        {
          "type": "extension",
          "name": "some_extension",
          "addon": "addon_a",
          "extension_group": "some_group"
        },
        {
          "type": "extension",
          "name": "another_ext",
          "addon": "addon_b",
          "extension_group": "some_group"
        }
      ],
      "connections": [
        {
          "extension": "some_extension",
          "cmd": [
            {
              "name": "hello",
              "dest": [
                {
                  "extension": "another_ext"
                }
              ]
            },
             {
              "name": "hello",
              "dest": [
                {
                  "extension": "some_extension"
                }
              ]
            }
          ]
        }
      ]
    }' --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    正在检查图[0]... ❌。详情：
      - connection[0]:
       - 将以下 cmd 合并为一个部分：
          “hello”在 flow[0] 和 flow[1] 中定义。

    全部完成。
    ❌  错误：1/1 个图失败。
    ```

### 7. 连接中声明的消息应兼容

将根据扩展的 manifest.json 中的架构定义检查连接中每个消息流中声明的消息是否与架构兼容。消息兼容的规则如下。

- 如果在源扩展和目标扩展中都找不到消息架构，则消息是兼容的。
- 如果仅在其中一个扩展中找到消息架构，则消息不兼容。
- 只有满足以下条件，消息才是兼容的。

  -   属性类型兼容。
      -   如果属性是一个对象，则源架构和目标架构中的字段都必须具有兼容的类型。
      -   如果 `required` 关键字在目标架构中定义，则源架构中必须有一个 `required` 关键字，它是目标中 `required` 的超集。

-   **示例**：

    假设已安装所有软件包。

    `addon_a` 中 manifest.json 的内容如下。

    ```json
    {
      "type": "extension",
      "name": "addon_a",
      "version": "0.1.0",
      "dependencies": [
        {
          "type": "system",
          "name": "ten_runtime_go",
          "version": "0.1.0"
        }
      ],
      "package": {
        "include": ["**"]
      },
      "api": {
        "cmd_out": [
          {
            "name": "cmd_1",
            "property": {
              "foo": {
                "type": "string"
              }
            }
          }
        ]
      }
    }
    ```

    并且，`addon_b` 中 manifest.json 的内容如下。

    ```json
    {
      "type": "extension",
      "name": "addon_b",
      "version": "0.1.0",
      "dependencies": [
        {
          "type": "system",
          "name": "ten_runtime_go",
          "version": "0.1.0"
        }
      ],
      "package": {
        "include": ["**"]
      },
      "api": {
        "cmd_in": [
          {
            "name": "cmd_1",
            "property": {
              "foo": {
                "type": "int8"
              }
            }
          }
        ]
      }
    }
    ```

    使用以下命令检查图。

    ```shell
    tman check graph --graph '{
      "nodes": [
        {
          "type": "extension",
          "name": "some_extension",
          "addon": "addon_a",
          "extension_group": "some_group"
        },
        {
          "type": "extension",
          "name": "another_ext",
          "addon": "addon_b",
          "extension_group": "some_group"
        }
      ],
      "connections": [
        {
          "extension": "some_extension",
          "cmd": [
            {
              "name": "cmd_1",
              "dest": [
                {
                  "extension": "another_ext"
                }
              ]
            }
          ]
        }
      ]
    }' --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    正在检查图[0]... ❌。详情：
      - connections[0]:
        - cmd[0]:  架构与 [extension_group: some_group, extension: another_ext] 不兼容，{ .foo: 类型不兼容，源为 [string]，但目标为 [int8] }

    全部完成。
    ❌ 错误：1/1 个图失败。
    ```

### 8. 节点中的 `app` 必须明确

每个节点中的 `app` 字段必须满足以下规则。

-   `app` 字段必须等于相应 TEN 应用程序的 `_ten::uri`。
-   所有节点都应声明 `app`，或者都不应声明。
-   `app` 字段不能为 `localhost`。
-   `app` 字段不能为空字符串。

-   **示例（某些节点指定了 `app` 字段）**：

    使用以下命令检查图。

    ```shell
    tman check graph --graph '{
      "nodes": [
        {
          "type": "extension",
          "name": "some_extension",
          "addon": "addon_a",
          "extension_group": "some_group"
        },
        {
          "type": "extension",
          "name": "another_ext",
          "addon": "addon_b",
          "extension_group": "some_group",
          "app": "http://localhost:8000"
        }
      ]
    }' --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    ❌  错误：图 JSON 字符串无效

    原因：
      所有节点都应该声明“app”，或者都不应该声明，而不是两者混合。
    ```

-   **示例（所有节点中均未指定 `app`，但某些源扩展在连接中指定了 `app`）**：

    使用以下命令检查图。

    ```shell
    tman check graph --graph '{
      "nodes": [
        {
          "type": "extension",
          "name": "some_extension",
          "addon": "addon_a",
          "extension_group": "some_group"
        },
        {
          "type": "extension",
          "name": "another_ext",
          "addon": "addon_b",
          "extension_group": "some_group"
        }
      ],
      "connections": [
       {
          "extension": "some_extension",
          "app": "http://localhost:8000",
          "cmd": [
            {
              "name": "cmd_1",
              "dest": [
                {
                  "extension": "another_ext"
                }
              ]
            }
          ]
        }
      ]
    }' --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    ❌  错误：图 JSON 字符串无效

    原因：
      connections[0]。不应声明“app”，因为没有任何节点声明它
    ```

-   **示例（所有节点中均未指定 `app`，但某些目标扩展在连接中指定了 `app`）**：

    使用以下命令检查图。

    ```shell
    tman check graph --graph '{
      "nodes": [
        {
          "type": "extension",
          "name": "some_extension",
          "addon": "addon_a",
          "extension_group": "some_group"
        },
        {
          "type": "extension",
          "name": "another_ext",
          "addon": "addon_b",
          "extension_group": "some_group"
        }
      ],
      "connections": [
        {
          "extension": "some_extension",
          "cmd": [
            {
              "name": "cmd_1",
              "dest": [
                {
                  "extension": "another_ext",
                  "app": "http://localhost:8000"
                }
              ]
            }
          ]
        }
      ]
    }' --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    ❌  错误：图 JSON 字符串无效

    原因：
      connections[0].cmd[0].dest[0]：不应声明“app”，因为没有任何节点声明它
    ```

-   **示例（节点中的 `app` 字段不等于应用程序的 `_ten::uri`）**：

    与 [规则 4](#id-4.-the-addons-declared-in-the-nodes-must-be-installed-in-the-app) 相同。

-   **示例（在单应用程序图中，`app` 字段为 `localhost`）**：

    使用以下命令检查图。

    ```shell
    tman check graph --graph '{
      "nodes": [
        {
          "type": "extension",
          "name": "some_extension",
          "addon": "addon_a",
          "extension_group": "some_group"
        },
        {
          "type": "extension",
          "name": "another_ext",
          "addon": "addon_b",
          "extension_group": "some_group",
          "app": "localhost"
        }
      ],
      "connections": [
        {
          "extension": "some_extension",
          "cmd": [
            {
              "name": "cmd_1",
              "dest": [
                {
                  "extension": "another_ext"
                }
              ]
            }
          ]
        }
      ]
    }' --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    ❌  错误：解析图字符串失败，nodes[1]：图定义中不允许使用“localhost”，并且该图似乎是单应用程序图，请删除“app”字段
    ```

-   **示例（在多应用程序图中，`app` 字段为 `localhost`）**：

    使用以下命令检查图。

    ```shell
    tman check graph --graph '{
      "nodes": [
        {
          "type": "extension",
          "name": "some_extension",
          "addon": "addon_a",
          "extension_group": "some_group",
          "app": "http://localhost:8000"
        },
        {
          "type": "extension",
          "name": "another_ext",
          "addon": "addon_b",
          "extension_group": "some_group",
          "app": "localhost"
        }
      ],
      "connections": [
        {
          "extension": "some_extension",
          "app": "http://localhost:8000",
          "cmd": [
            {
              "name": "cmd_1",
              "dest": [
                {
                  "extension": "another_ext"
                }
              ]
            }
          ]
        }
      ]
    }' --app /home/TEN-Agent/agents
    ```

    **输出**：

    ```text
    ❌  错误：解析图字符串失败，nodes[1]：图定义中不允许使用“localhost”，请将“app”字段的内容更改为与“_ten::uri”一致
    ```
