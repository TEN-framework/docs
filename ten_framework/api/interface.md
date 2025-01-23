# 接口

TEN 框架的消息系统提供了一种在单个消息粒度上进行交互的机制。然而，在实践中，一个完整的功能通常涉及多个消息，例如多个命令、数据交换等。例如，语音转文本 (STT) 功能可能需要多个命令和数据消息。这些固定的消息组合形成了所谓的**接口**。如果一个扩展支持一个接口定义的所有消息，则可以在任何需要该接口的上下文中使用它。

TEN 框架没有要求扩展在其清单中显式声明对接口中每个消息的支持（这将很麻烦），而是引入了**聚合**这些消息的概念。这允许扩展声明它们提供或使用特定的接口，从而简化了流程。

TEN 框架提供的以下 API 是原始的：

-   `cmd_in`
-   `cmd_out`
-   `data_in`
-   `data_out`
-   `audio_frame_in`
-   `audio_frame_out`
-   `video_frame_in`
-   `video_frame_out`

除此之外，该框架还支持复合 API 机制：

-   `interface_in`
-   `interface_out`

## 接口语法

定义接口的基本语法包括一个强制性的 `name` 字段。此 `name` 的作用与 `cmd_in` 和 `cmd_out` 中的 `name` 字段相同，表示接口的名称。它仅在扩展的上下文中具有意义，并且在该范围内必须是唯一的。

{% code title=".json" %}

```json
{
  "api": {
    "interface_in": [
      {
        "name": "foo"
      }
    ],
    "interface_out": [
      {
        "name": "foo"
      }
    ]
  }
}
```

{% endcode %}

## 在图中使用接口

接口的 `name` 主要在图中使用以指定路由。在下面的示例中，`src_extension` 使用 `dest_extension` 提供的 `foo` 接口。`src_extension` 从其 `interface_out` 定义中识别 `foo` 接口，而 `dest_extension` 从其 `interface_in` 定义中识别 `foo` 接口。

{% code title=".json" %}

```json
{
  "predefined_graphs": [{
    "name": "default",
    "auto_start": true,
    "nodes": [
      {
        "type": "extension_group",
        "name": "default_extension_group",
        "addon": "default_extension_group"
      },
      {
        "type": "extension",
        "name": "src_extension",
        "addon": "src_extension",
        "extension_group": "default_extension_group"
      },
      {
        "type": "extension",
        "name": "dest_extension",
        "addon": "dest_extension",
        "extension_group": "default_extension_group"
      }
    ],
    "connections": [{
      "extension": "src_extension",
      "interface": [{
        "name": "foo",
        "dest": [{
          "extension": "dest_extension"
        }]
      }]
    }]
  }]
}
```

{% endcode %}

## `interface_in` 和 `interface_out` 的含义

1.  **`interface_in`**

    表示扩展支持指定的接口的功能。

{% code title=".json" %}

   ```json
   {
     "api": {
       "interface_in": [
         {
           "name": "foo"
           // 接口内容
         }
       ]
     }
   }
   ```

{% endcode %}

2.  **`interface_out`**

    表示扩展需要另一个扩展来提供指定接口的功能。

{% code title=".json" %}

   ```json
   {
     "api": {
       "interface_out": [
         {
           "name": "foo"
           // 接口内容
         }
       ]
     }
   }
   ```

{% endcode %}

## 接口和消息声明

当扩展在其清单中声明一个接口时，它会隐式声明该接口中定义的所有消息。例如，如果一个接口 `foo` 定义了三个命令，则：

-   **`interface_in`**：该扩展提供这三个命令作为接口的一部分。
-   **`interface_out`**：该扩展需要从另一个扩展接收这三个命令。

从这个意义上说，接口机制充当**语法糖**，允许预定义的一组消息被捆绑并在多个扩展之间重用。

如果扩展在其清单中定义了一个接口，则无需单独定义该接口中包含的消息。当扩展发送或接收像 `foo` 这样的命令时，TEN 运行时会在扩展的清单中查找相关的接口定义并相应地使用该命令。

例如，如果一个接口定义了三个命令和一个数据消息，则在 `interface_in` 中声明此接口的扩展将隐式地在 `cmd_in` 中包含这三个命令，并在 `data_in` 中包含数据消息。类似地，如果在 `interface_out` 中声明了该接口，则这些命令将包含在 `cmd_out` 中，并且数据消息将包含在 `data_out` 中。

## 支持具有相同消息名称的多个接口

在当前设计中，扩展不能在单个 API 项下声明对具有相同消息名称的两个接口的支持。例如，如果 `foo` 和 `bar` 接口都定义了一个名为 `xxx` 的命令，则以下组合要么允许，要么不允许：

-   不允许

{% code title=".json" %}

  ```json
  {
    "api": {
      "interface_in": [
        {
          "name": "foo"
          // 接口 foo 内容
        },
        {
          "name": "bar"
          // 接口 bar 内容
        }
      ]
    }
  }
  ```

{% endcode %}

-   允许

{% code title=".json" %}

  ```json
  {
    "api": {
      "interface_out": [
        {
          "name": "foo"
          // 接口 foo 内容
        },
        {
          "name": "bar"
          // 接口 bar 内容
        }
      ]
    }
  }
  ```

{% endcode %}

{% tab title="Allowed" %}

{% code title=".json" %}

  ```json
  {
    "api": {
      "interface_in": [
        {
          "name": "foo"
          // 接口 foo 内容
        }
      ],
      "interface_out": [
        {
          "name": "bar"
          // 接口 bar 内容
        }
      ]
    }
  }
  ```

{% endcode %}
{% endtab %}

## 接口定义内容

`interface` 的定义类似于清单中的 `api` 字段。以下是 `interface` 定义的示例：

{% code title=".json" %}

```json
{
  "cmd": [
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
  "data": [
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
  "video_frame": [],
  "audio_frame": []
}
```

{% endcode %}

TEN 框架通过将此接口的定义集成到扩展的清单中，位于 `api` 字段下，来处理此接口。例如，`interface_in` 中定义的命令将集成到 `cmd_in` 中，而 `interface_out` 中定义的命令将集成到 `cmd_out` 中。

## 指定接口内容

有两种方法可以指定接口的内容：

{% tabs %}
{% tab title="直接嵌入内容" %}

直接将接口定义嵌入到清单中。示例：
{% code title=".json" %}

```json
{
  "api": {
    "interface_in": [
      {
        "name": "foo",
        "cmd": [],
        "data": [],
        "video_frame": [],
        "audio_frame": []
      }
    ]
  }
}
```

{% endcode %}
{% endtab %}

{% tab title="引用内容" %}

使用引用来指定接口定义，类似于 JSON 架构中的 `$ref` 语法。

{% code title=".json" %}

```json
{
  "api": {
    "interface_in": [
      {
        "name": "foo",
        "$ref": "http://www.github.com/darth_vader/fight.json"
      }
    ]
  }
}
```

{% endcode %}
{% endtab %}
{% endtabs %}

## 确定接口兼容性

由于接口本质上是语法糖，因此两个接口是否可以连接取决于底层消息的兼容性。当源扩展指定输出接口 `foo`，而目标扩展指定输入接口 `bar` 时，TEN 运行时会检查源的 `foo` 接口是否可以连接到目标的 `bar` 接口。

{% code title=".json" %}

```json
{
  "api": {
    "interface_out": "foo"
    // 接口内容
  }
}
```

{% endcode %}

{% code title=".json" %}

```json
{
  "api": {
    "interface_in": "bar"
    // 接口内容
  }
}
```

{% endcode %}

{% code title=".json" %}

```json
{
  "extension_group": {
    "addon": "default_extension_group",
    "name": "default_extension_group"
  },
  "extension": {
    "addon": "extension_A",
    "name": "extension_A"
  },
  "interface": [
    {
      "name": "foo",
      "dest": [
        {
          "extension_group": {
            "addon": "default_extension_group",
            "name": "default_extension_group"
          },
          "extension": {
            "addon": "dest_extension",
            "name": "dest_extension"
          }
        }
      ]
    }
  ]
}
```

{% endcode %}

TEN 框架将在源扩展清单的 `interface_out` 部分中查找 `foo` 的定义。然后，它会根据 TEN 框架的架构检查规则，针对目标扩展的清单（包括其消息定义以及在 `interface_in` 中声明的任何接口）检查此接口中定义的每个消息。如果任何消息无法匹配，则图配置将被视为无效。
