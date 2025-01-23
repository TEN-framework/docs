# 图

在 TEN 框架中，有两种类型的图：

1.  灵活
2.  预定义

|       | 灵活                                                       | 预定义                                                           |
| ----- | ---------------------------------------------------------- | ---------------------------------------------------------------- |
| 启动图的时间 | 当 TEN 应用程序收到 `start_graph` 命令时。                                   | 当 TEN 应用程序启动时，或当 TEN 应用程序收到 `start_graph` 命令时。                          |
| 图的内容   | 在 `start_graph` 命令中指定。                                           | 在 TEN 应用程序的属性中指定。                                                       |
| 图的 ID   | 随机 UUID。                                                    | 随机 UUID                                                        |

<figure><img src="../assets/png/two_types_of_graph.png" alt=""><figcaption><p>两种类型的图</p></figcaption></figure>

对于预定义的图，有一个 `auto_start` 属性，用于确定预定义的图是否在 TEN 应用程序启动时自动启动。

还有另一个 `singleton` 属性用于指示预定义的图是否只能在 TEN 应用程序中生成 *一个* 对应的引擎实例。

## 图的 ID 和图的名称

对于从图定义生成的每个图实例，即 TEN 应用程序中的引擎实例，都有一个唯一的 UUID4 字符串表示每个图实例。此 UUID4 字符串称为该图的 **图 ID**。

对于每个预定义的图，可以分配一个有意义或易于记忆的名称，称为预定义图的 **图名称**。在指定特定的预定义图时，可以使用图名称来表示它。如果预定义的图具有 singleton 属性，则意味着从此预定义图生成的图实例只能在 TEN 应用程序中存在一次。因此，TEN 运行时将使用图名称来唯一标识从 singleton 预定义图生成的单个图实例。

## 灵活的图

当 TEN 应用程序收到 `start_graph` 命令并创建此类型的图时，它将分配一个随机 UUID 值作为新启动的图的 ID。如果其他客户端获取了此图的 ID，它们也可以连接到此图。

灵活的图 ID 示例：

`123e4567-e89b-12d3-a456-426614174000`

## 预定义的图

预定义的图与灵活的图非常相似。灵活图的内容包含在 `start_graph` 命令中，而预定义图的内容由 TEN 应用程序定义。客户端只需在 `start_graph` 命令中指定它们想要启动的预定义图的名称。

预定义图的主要目的是为了易于使用和信息保护。预定义图允许客户端避免了解图的详细内容，这可能是由于可用性考虑或为了防止客户端访问图包含的某些信息。

预定义的图名称示例：

`http-server`

当 TEN 应用程序启动时，设置为自动启动的预定义图也将被启动。

## 图的定义

图的定义（无论是灵活的还是预定义的）都是相同的。以下是图的定义：

```json
{
  "nodes": [
    // 节点的定义
  ],
  "connections": [
    // 连接的定义
  ]
}
```

要点：

1.  如果只有一个应用程序，则可以省略 app 字段。否则，必须指定。如果只有一个应用程序且未指定 app 字段，则 TEN 运行时将默认使用 `localhost` 作为 app 字段。

2.  nodes 字段指定图中的节点，例如扩展、扩展组等。

3.  对于图中的每个节点，它只能在 nodes 字段中出现一次。如果多次出现，TEN 框架将在 TEN 管理器进行的图验证期间或 TEN 运行时进行的图验证期间抛出错误。

4.  在 nodes 字段中指定扩展组的方式如下。

    property 字段是可选的。

    ```json
    {
      "type": "extension_group",
      "name": "default_extension_group",
      "addon": "default_extension_group",
      "app": "msgpack://127.0.0.1:8001/",
      "property": {
        "root_key": "player",
        "extra_keys": [
          "playerName"
        ]
      }
    }
    ```

5.  在 nodes 字段中指定扩展的方式如下。

    property 字段是可选的。addon 字段也是可选的。

    *   如果存在 addon 字段，则表示该扩展是由该插件生成的实例。
    *   如果不存在 addon 字段，则表示该扩展不是由插件生成的，而是由相应的扩展组创建的。在这种情况下，通常不需要在 nodes 字段中显式定义它，但如果您想指定其 property 字段，则必须在 nodes 字段中显式定义它。

    ```json
    {
      "type": "extension",
      "name": "simple_http_server_cpp",
      "addon": "simple_http_server_cpp",
      "extension_group": "default_extension_group",
      "app": "msgpack://127.0.0.1:8001/",
      "property": {
        "root_key": "player",
        "extra_keys": [
          "playerName"
        ]
      }
    }
    ```

6.  connections 字段指定图中节点之间的连接。

    在每个连接中，extension 和 extension group 的值都是表示相应节点名称的字符串。

一个完整的示例是：

```json
{
  "nodes": [
    {
      "type": "extension_group",
      "name": "default_extension_group",
      "addon": "default_extension_group",
      "app": "msgpack://127.0.0.1:8001/"
    },
    {
      "type": "extension",
      "name": "simple_http_server_cpp",
      "addon": "simple_http_server_cpp",
      "extension_group": "default_extension_group",
      "property": {
        "root_key": "player",
        "extra_keys": [
          "playerName"
        ]
      }
    }
  ],
  "connections": [
    {
      "app": "msgpack://127.0.0.1:8001/",
      "extension": "simple_http_server_cpp",
      "cmd": [
        {
          "name": "start",
          "dest": [
            {
              "app": "msgpack://127.0.0.1:8001/",
              "extension": "gateway"
            }
          ]
        },
        {
          "name": "stop",
          "dest": [
            {
              "app": "msgpack://127.0.0.1:8001/",
              "extension": "gateway"
            }
          ]
        }
      ]
    },
    {
      "app": "msgpack://127.0.0.1:8001/",
      "extension": "gateway",
      "cmd": [
        {
          "name": "push_status_online",
          "dest": [
            {
              "app": "msgpack://127.0.0.1:8001/",
              "extension": "uap"
            }
          ]
        }
      ]
    }
  ]
}
```

## 预定义图的定义

本质上，您将上面的完整图定义放置在应用程序属性中的 `predefined_graphs` 字段下。`predefined_graphs` 字段也将具有其属性，例如 name、auto_start 等。

```json
"predefined_graphs": [
  {
    "name": "default",
    "auto_start": true,
    "singleton": true,
    // 将完整的图定义放在此处。
  }
]
```

所以它看起来像这样：

```json
"predefined_graphs": [
  {
    "name": "default",
    "auto_start": true,
    "singleton": true,
    "nodes": [
      {
        "type": "extension_group",
        "name": "default_extension_group",
        "addon": "default_extension_group",
        "app": "msgpack://127.0.0.1:8001/"
      },
      {
        "type": "extension",
        "name": "simple_http_server_cpp",
        "addon": "simple_http_server_cpp",
        "extension_group": "default_extension_group",
        "property": {
          "root_key": "player",
          "extra_keys": [
            "playerName"
          ]
        }
      }
    ],
    "connections": [
      {
        "app": "msgpack://127.0.0.1:8001/",
        "extension": "simple_http_server_cpp",
        "cmd": [
          {
            "name": "start",
            "dest": [
              {
                "app": "msgpack://127.0.0.1:8001/",
                "extension": "gateway"
              }
            ]
          },
          {
            "name": "stop",
            "dest": [
              {
                "app": "msgpack://127.0.0.1:8001/",
                "extension": "gateway"
              }
            ]
          }
        ]
      },
      {
        "app": "msgpack://127.0.0.1:8001/",
        "extension": "gateway",
        "cmd": [
          {
            "name": "push_status_online",
            "dest": [
              {
                "app": "msgpack://127.0.0.1:8001/",
                "extension": "uap"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## `start_graph` 命令的定义

本质上，您将上面的完整图定义放置在 `start_graph` 命令的 `ten` 字段下。`start_graph` 命令也将具有其属性，例如 type、seq_id 等。

```json
{
  "_ten": {
    "type": "start_graph",
    "seq_id": "55"
    // 将完整的图定义放在此处。
  }
}
```

以下是 `start_graph` 命令的完整定义：

```json
{
  "_ten": {
    "type": "start_graph",
    "seq_id": "55",
    "nodes": [
      {
        "type": "extension_group",
        "name": "default_extension_group",
        "addon": "default_extension_group",
        "app": "msgpack://127.0.0.1:8001/"
      },
      {
        "type": "extension",
        "name": "simple_http_server_cpp",
        "addon": "simple_http_server_cpp",
        "extension_group": "default_extension_group",
        "property": {
          "root_key": "player",
          "extra_keys": [
            "playerName"
          ]
        }
      }
    ],
    "connections": [
      {
        "app": "msgpack://127.0.0.1:8001/",
        "extension": "simple_http_server_cpp",
        "cmd": [
          {
            "name": "start",
            "dest": [
              {
                "app": "msgpack://127.0.0.1:8001/",
                "extension": "gateway"
              }
            ]
          },
          {
            "name": "stop",
            "dest": [
              {
                "app": "msgpack://127.0.0.1:8001/",
                "extension": "gateway"
              }
            ]
          }
        ]
      },
      {
        "extension": "gateway",
        "cmd": [
          {
            "name": "push_status_online",
            "dest": [
              {
                "extension": "uap"
              }
            ]
          }
        ]
      }
    ]
  }
}
```

## 图定义的规范

-   **`nodes` 字段的要求**：
    在图定义中，`nodes` 数组是强制性的。相反，`connections` 数组是可选的，但鼓励用于定义节点间通信。

-   **节点 `app` 字段的验证**：
    在任何情况下，`app` 字段都不能设置为 `localhost`。在单应用程序图中，不应指定 `app` URI。在多应用程序图中，`app` 字段的值必须与每个应用程序的 `property.json` 中定义的 `_ten::uri` 值匹配。

-   **节点唯一性和标识**：
    `nodes` 数组中的每个节点都表示应用程序组中的特定扩展实例，该实例由指定的插件创建。因此，每个扩展实例都应由单个节点唯一表示。节点必须通过 `app`、`extension_group` 和 `name` 的组合来唯一标识。不允许同一扩展实例的多个条目。以下示例无效，因为它为同一扩展实例定义了多个节点：

    ```json
    {
      "nodes": [
        {
          "type": "extension",
          "name": "some_ext",
          "addon": "addon_1",
          "extension_group": "test"
        },
        {
          "type": "extension",
          "name": "some_ext",
          "addon": "addon_2",
          "extension_group": "test"
        }
      ]
    }
    ```

-   **连接中扩展实例定义的一致性**：
    在 `connections` 字段中引用的所有扩展实例（无论是作为源还是目标）都必须在 `nodes` 字段中显式定义。任何未在 `nodes` 数组中定义的实例都将导致验证错误。

    例如，以下示例无效，因为扩展实例 `ext_2` 在 `connections` 字段中使用，但未在 `nodes` 字段中定义：

    ```json
    {
      "nodes": [
        {
          "type": "extension",
          "name": "ext_1",
          "addon": "addon_1",
          "extension_group": "some_group"
        }
      ],
      "connections": [
        {
          "extension": "ext_1",
          "cmd": [
            {
              "name": "hello",
              "dest": [
                {
                  "extension": "ext_2"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

-   **连接定义的整合**：
    在 `connections` 数组中，与同一源扩展实例相关的所有消息必须分组在单个部分中。将同一源扩展实例的信息拆分为多个部分会导致不一致和错误。

    例如，以下示例不正确，因为来自 `ext_1` 的消息被分为不同的部分：

    ```json
    {
      "connections": [
        {
          "extension": "ext_1",
          "cmd": [
            {
              "name": "hello",
              "dest": [
                {
                  "extension": "ext_2"
                }
              ]
            }
          ]
        },
        {
          "extension": "ext_1",
          "data": [
            {
              "name": "hello",
              "dest": [
                {
                  "extension": "ext_2"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

    正确的方法是将同一源扩展实例的所有消息整合到一个部分中：

    ```json
    {
      "connections": [
        {
          "extension": "ext_1",
          "cmd": [
            {
              "name": "hello",
              "dest": [
                {
                  "extension": "ext_2"
                }
              ]
            }
          ],
          "data": [
            {
              "name": "hello",
              "dest": [
                {
                  "extension": "ext_2"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

-   **唯一消息的目标整合**：
    对于特定类型（例如，`cmd` 或 `data`）中的每条消息，目标扩展实例必须在该消息的单个条目下分组。重复具有单独目标的相同消息名称会导致不一致和验证错误。

    例如，由于名为 `hello` 的消息的单独条目，以下示例不正确：

    ```json
    {
      "connections": [
        {
          "extension": "ext_1",
          "cmd": [
            {
              "name": "hello",
              "dest": [
                {
                  "extension": "ext_2"
                }
              ]
            },
            {
              "name": "hello",
              "dest": [
                {
                  "extension": "ext_3"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

    正确的方法是将同一消息的所有目标整合到一个条目下：

    ```json
    {
      "connections": [
        {
          "extension": "ext_1",
          "cmd": [
            {
              "name": "hello",
              "dest": [
                {
                  "extension": "ext_2"
                },
                {
                  "extension": "ext_3"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

    但是，具有相同名称的消息可以跨不同类型（例如，`cmd` 和 `data`）存在，而不会导致冲突。

有关更多示例，请参阅 TEN 框架 `tman` 中的 `check graph` 命令文档。
