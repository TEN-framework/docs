---
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
# 创建一个 “Hello World” 扩展

在本章中，我们将逐步创建一个 “Hello World” 扩展，该扩展将以 Python、Go 和 C++ 呈现。您可以随意选择您喜欢的任何语言。准备好了吗？

## 前提条件

在深入本章之前，您需要熟悉[前面介绍的基础知识](getting_started.md)。特别是，请确保您了解如何使用 `docker compose up` 并了解后台运行的服务。

## 1. 启动服务器

首先，让我们先启动服务器。运行以下命令：

{% hint style="info" %}
如果标题显示“终端”，则表示您正在本地运行命令。如果标题显示“Bash”，则表示您正在 Docker 容器中运行命令。
{% endhint %}

{% code title=">_ 终端" %}

```bash
docker compose up
```

{% endcode %}

输入命令后，您应该看到类似于以下内容的输出：

<pre class="language-bash" data-title=">_ 终端"><code class="lang-bash">....
Attaching to ten_agent_dev, ten_agent_playground
ten_agent_dev         | cd agents && tman designer
ten_agent_dev         | :-)  Starting server at http://0.0.0.0:49483
ten_agent_playground  |   ▲ Next.js 14.2.4
ten_agent_playground  |   - Local:        http://localhost:3000
ten_agent_playground  |   - Network:      http://0.0.0.0:3000
ten_agent_playground  |
ten_agent_playground  |  ✓ Starting...
ten_agent_playground  |  ✓ Ready in 429ms
...
</code></pre>

现在，我们正在运行以下服务：

• `ten_agent_dev`，地址为 `http://0.0.0.0:49483`（开发服务器）

• `ten_agent_playground`，地址为 `http://localhost:3000` (TEN Agent playground)

## 2. 进入 Docker 容器

要在隔离的环境中工作，请运行以下命令：

{% code title=">_ 终端" %}

```bash
docker exec -it ten_agent_dev bash
```

{% endcode %}

## 3. 创建 Hello World 扩展

通过运行以下命令，将在 Python、Go 或 C++ 中创建一个名为 `hello_world` 的扩展。

{% tabs %}
{% tab title="Python" %}

<pre class="language-bash" data-title=">_ Bash" data-overflow="wrap"><code class="lang-bash">
<strong>
cd /app/agents/ten_packages/extension
tman create extension hello_world --template=default_async_extension_python@0.6 --template-data class_name_prefix=HelloWorld
</strong>
</code></pre>

{% endtab %}

{% tab title="Go" %}

<pre class="language-bash" data-title=">_ Bash" data-overflow="wrap"><code class="lang-bash">
<strong>
cd /app/agents/ten_packages/extension
tman create extension hello_world --template=default_extension_go@0.6 --template-data class_name_prefix=HelloWorld
</strong>
</code></pre>

{% endtab %}

{% tab title="C++" %}

<pre class="language-bash" data-title=">_ Bash" data-overflow="wrap"><code class="lang-bash">
<strong>
cd /app/agents/ten_packages/extension
tman create extension hello_world --template=default_extension_cpp@0.6 --template-data class_name_prefix=HelloWorld
</strong>
</code></pre>

{% endtab %}
{% endtabs %}

运行命令后，日志将显示如下内容：

<pre class="language-bash" data-title=">_ Bash"><code class="lang-bash">
Package 'extension:hello_world' created successfully in '/app' in 3 seconds.
</code></pre>

## 4. 向扩展添加 API

导航到 `hello_world` 目录并打开 manifest.json。添加带有 `data_in` 和 `cmd_out` 的 API 对象：

<pre class="language-json" data-title="./hello_world/manifest.json"><code class="lang-json">{
  "type": "extension",
  "name": "hello_world",
  "version": "0.3.1",
  "dependencies": [
    {
      "type": "system",
      "name": "ten_runtime_python",
      "version": "0.3.1"
    }
  ],
  "package": {
    "include": [
      "manifest.json",
      "property.json",
      "BUILD.gn",
      "**.tent",
      "**.py",
      "README.md",
      "tests/**"
    ]
  },
  "api": {
<strong>    "data_in": [
</strong><strong>      {
</strong><strong>        "name": "text_data",
</strong><strong>        "property": {
</strong><strong>          "text": {
</strong><strong>            "type": "string"
</strong><strong>          },
</strong><strong>          "is_final": {
</strong><strong>            "type": "bool"
</strong><strong>          }
</strong><strong>        }
</strong><strong>      }
</strong><strong>    ],
</strong><strong>    "cmd_out": [
</strong><strong>      {
</strong><strong>        "name": "flush"
</strong><strong>      }
</strong><strong>    ]
</strong>  }
}
</code></pre>

## 5. 构建扩展

让我们使用 `cd /app` 命令返回到项目的根目录，并运行 `make build` 来构建扩展。

{% code title=">_ Bash" %}

```bash
cd /app

task use
```

{% endcode %}

## 6. 重启服务器

当您第一次构建智能体时，无需重启服务器。但是，在进行细微更新后，如果刷新页面没有应用更改，您将需要在 Docker 中重启服务器以确保更新生效。

<figure><img src="../assets/gif/docker_restart_server.gif" alt=""><figcaption><p>重启 ten_agent_dev 的服务器</p></figcaption></figure>

## 7. 验证扩展

恭喜！您已成功创建了第一个 `hello_world` 扩展。
