# Required

在 TEN 框架中，只允许消息架构具有 `required` 字段，并且目前不允许扩展的属性包含 `required` 字段。

这意味着允许 `cmd_in`、`cmd_out`、`data_in`、`data_out`、`audio_frame_in`、`audio_frame_out`、`video_frame_in` 和 `video_frame_out` 的架构具有 `required` 字段。

对于消息架构，`required` 字段只能出现在三个特定的位置：

-   与 `<foo>_in` / `<foo>_out` 中的 `property` 处于同一级别。
-   在 `<foo>_in` / `<foo>_out` 中 `result` 的 `property` 内。
-   在类型为 `object` 的 `property` 内部（即，在嵌套的情况下）。

下面显示了这三种情况的示例。

```json
{
  "api": {
    "cmd_in": [
      {
        "name": "foo",
        "property": {
          "a": {
            "type": "int8"
          },
          "b": {
            "type": "uint8"
          },
          "c": {
            "type": "array",
            "items": {
              "type": "string"
            }
          },
          "d": {
            "type": "object",
            "properties": {
              "e": {
                "type": "float32"
              }
            }
          },
          "exampleObject": {
            "type": "object",
            "properties": {
              "foo": {
                "type": "int32"
              },
              "bar": {
                "type": "string"
              }
            },
            "required": ["foo"]  // 3.
          }
        },
        "required": ["a", "b"],  // 1.
        "result": {
          "property": {
            "ccc": {
              "type": "buf"
            },
            "detail": {
              "type": "buf"
            }
          },
          "required": ["ccc"]  // 2.
        }
      }
    ]
  }
}
```

## `required` 的使用

### 当消息从扩展发送时

当扩展调用 `send_<foo>(msg_X)` 或 `return_result(result_Y)` 时，框架会根据扩展中各自的架构检查 `msg_X` 或 `result_Y`。如果 `msg_X` 或 `result_Y` 缺少架构中标记为 `required` 的任何字段，则架构检查将失败，指示出现错误。

这三种情况的处理方式是相同的，尽管会分别讨论：

1.  如果 `send_<foo>` 发送 TEN 命令并且架构检查失败：

    `send_<foo>` 将立即返回 false，并且如果提供了错误参数，它将包括架构检查失败错误消息。

2.  如果 `return_result` 未通过架构检查：

    `return_result` 将返回 false，并且如果提供了错误参数，它将包括架构检查失败错误消息。

3.  如果 `send_<foo>` 发送类似数据的一般 TEN 消息（例如数据、音频帧或视频帧）：

    `send_<foo>` 将返回 false，并且如果提供了错误参数，它将包括架构检查失败错误消息。

### 当消息被扩展接收时

在 ten\_runtime 将 `msg_X` 或 `result_Y` 传递给扩展的 `on_<foo>()` 或结果处理程序之前，它会检查 `msg_X` 或 `result_Y` 的架构中定义的所有 `required` 字段是否存在。如果缺少任何 `required` 字段，则架构检查将失败。

1.  如果传入的消息是 TEN 命令：

    ten\_runtime 将向之前的扩展返回一个错误 `status_code` 结果。

2.  如果传入的消息是 TEN 命令结果：

    ten\_runtime 会将结果的 `status_code` 更改为错误，添加缺少的 `required` 字段，并根据这些字段的类型将其值设置为其默认值。

3.  如果传入的消息是 TEN 类似数据的消息：

    ten\_runtime 将简单地删除类似数据的消息。

## 图检查的行为

TEN Manager 具有一个名为图检查的功能，用于验证图的语义正确性。与 required 字段相关的检查如下：

1.  对于连接，源的 `required` 字段必须是目标的 `required` 字段的超集。
2.  如果源和目标 `required` 字段中都出现相同的字段名称，则它们的类型必须兼容。
