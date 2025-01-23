# Web 服务器

Web 服务器是一个简单的 Golang Web 服务器，用于处理 HTTP 请求。它负责处理传入的请求并启动/停止代理进程。

已构建 Playground/演示 UI 来与 Web 服务器进行交互。您还可以使用 curl 或任何其他 HTTP 客户端与 Web 服务器进行交互。

## 请求与响应示例

该服务器提供一个用于管理代理进程的简单层。API 资源如下所述。

### API 资源

- [POST /start](#post-start)
- [POST /stop](#post-stop)
- [POST /ping](#post-ping)

### POST /start

此 API 使用给定的图和覆盖属性启动代理。启动的代理将加入到指定的频道，并订阅您的浏览器/设备的 RTC 用于加入的 UID。

| 参数         | 描述                                                                                                                             |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------ |
| request_id  | 用于跟踪目的的任何 UUID                                                                                                           |
| channel_name | 频道名称，需要与您的浏览器/设备加入的频道相同，代理需要与您的浏览器/设备在同一频道中进行通信                                                                               |
| user_uid    | 您的浏览器/设备的 RTC 用于加入的 UID，代理需要知道您的 RTC UID 才能订阅您的音频                                                                           |
| bot_uid    | 可选，机器人用于加入 RTC 的 UID                                                                                                   |
| graph_name  | 启动代理时要使用的图，将在 property.json 中查找                                                                                               |
| properties  | 要在 property.json 中覆盖的其他属性，覆盖不会更改原始的 property.json，仅更改代理启动时使用的属性                                                               |
| timeout     | 确定代理在未收到任何 ping 的情况下将保持活动状态的时间。如果超时设置为 `-1`，则代理不会因不活动而终止。默认情况下，超时设置为 60 秒，但可以使用 `.env` 文件中的 `WORKER_QUIT_TIMEOUT_SECONDS` 变量进行调整。 |

示例:

```bash
curl 'http://localhost:8080/start' \
  -H 'Content-Type: application/json' \
  --data-raw '{
    "request_id": "c1912182-924c-4d15-a8bb-85063343077c",
    "channel_name": "test",
    "user_uid": 176573,
    "graph_name": "camera_va_openai_azure",
    "properties": {
      "openai_chatgpt": {
        "model": "gpt-4o"
      }
    }
  }'
```

### POST /stop

此 API 停止您启动的代理。

| 参数         | 描述                                  |
| ----------- | ------------------------------------ |
| request_id  | 用于跟踪目的的任何 UUID                   |
| channel_name | 频道名称，即您用于启动代理的频道 |

示例:

```bash
curl 'http://localhost:8080/stop' \
  -H 'Content-Type: application/json' \
  --data-raw '{
    "request_id": "c1912182-924c-4d15-a8bb-85063343077c",
    "channel_name": "test"
  }'
```

### POST /ping

此 API 向服务器发送 ping，以表明连接仍然有效。如果在启动代理时指定 `timeout:-1`，则不需要此操作，否则如果未在超时（以秒为单位）后收到 ping，则代理将退出。

| 参数         | 描述                                  |
| ----------- | ------------------------------------ |
| request_id  | 用于跟踪目的的任何 UUID                   |
| channel_name | 频道名称，即您用于启动代理的频道 |

示例:

```bash
curl 'http://localhost:8080/ping' \
  -H 'Content-Type: application/json' \
  --data-raw '{
    "request_id": "c1912182-924c-4d15-a8bb-85063343077c",
    "channel_name": "test"
  }'
```
