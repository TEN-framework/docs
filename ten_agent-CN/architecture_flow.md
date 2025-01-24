# 架构

整个系统包含多个协同工作的组件，以提供无缝的体验。主要组件包括：

- **TEN Agent App**: 主要应用程序，负责协调扩展并管理它们之间的数据流。它作为后台进程运行。根据图的配置，agent 启动所需的扩展并处理它们之间的数据流。
- **前端 UI**: 一个基于 Web 的界面，用于与 agent 交互。它允许用户配置 agent、启动/停止 agent 以及与 agent 对话。
- **Web 服务器**: 一个简单的 Golang Web 服务器，用于处理 HTTP 请求。它负责处理传入的请求和启动/停止 agent 进程。它还传递诸如 `graph_name` 之类的参数来确定要使用的图。

系统的流程如下：

![架构流程](https://github.com/TEN-framework/docs/blob/main/assets/png/architecture_flow.png?raw=true)
