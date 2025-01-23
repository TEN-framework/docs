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

# 快速开始

在本章节中，让我们一起构建 TEN Agent 的 Playground。

## 前提条件

{% tabs %}
{% tab title="API Keys" %}

* Agora [ App ID ](https://docs.agora.io/en/video-calling/get-started/manage-agora-account?platform=web#create-an-agora-project) 和 [ App Certificate ](https://docs.agora.io/en/video-calling/get-started/manage-agora-account?platform=web#create-an-agora-project) (每月有免费额度)
<!-- * [OpenAI](https://openai.com/index/openai-api/) API key -->
<!-- * Azure [speech-to-text](https://azure.microsoft.com/en-us/products/ai-services/speech-to-text) 和 [text-to-speech](https://azure.microsoft.com/en-us/products/ai-services/text-to-speech) API 密钥 -->
{% endtab %}

{% tab title="安装" %}

* [Docker](https://www.docker.com/) / [Docker Compose](https://docs.docker.com/compose/)
* [Node.js(LTS) v18](https://nodejs.org/en)
{% endtab %}

{% tab title="最低系统要求" %}
:tada: CPU >= 2 核

:smile: 内存 >= 4 GB
{% endtab %}
{% endtabs %}

**Apple Silicon 上的 Docker 设置**

{% hint style="info" %}
对于 Apple Silicon Mac，请在 Docker 设置中取消选中 "Use Rosetta for x86/amd64 emulation"。注意：这可能会导致在 ARM 上的构建时间变慢，但在部署到 x64 服务器时性能将恢复正常。
{% endhint %}

<figure><img src="../assets/gif/docker_setting.gif" alt="" width="563"><figcaption><p>确保该复选框未被选中</p></figcaption></figure>

## 下一步

**1. 克隆 TEN Agent 仓库**

{% code title=">_ 终端" %}

```sh
git clone https://github.com/TEN-framework/TEN-Agent.git
```

{% endcode %}

**2. 准备配置文件**

在你的代码编辑器中打开 TEN Agent。在项目根目录中使用 `cd` 命令从示例创建 `.env` 文件。

{% code title=">_ 终端" %}

```sh
cp ./.env.example ./.env
```

{% endcode %}

**3. 在 .env 文件中设置 Agora App ID 和 App Certificate**

打开 `.env` 文件并填写 Agora App ID 和 App Certificate。这些将用于连接 Agora RTC 扩展。

{% code title=".env" %}

```bash
AGORA_APP_ID=
AGORA_APP_CERTIFICATE=
```

{% endcode %}

**4. 启动 agent 构建工具容器**

在同一目录下，运行 `docker` 命令来组合容器：

{% code title=">_ 终端" %}

```bash
docker compose up -d
```

{% endcode %}

**5. 进入容器**

使用以下命令进入容器：

{% code title=">_ Bash" %}

```bash
docker exec -it ten_agent_dev bash
```

{% endcode %}

**6. 构建 agent**

使用以下命令构建 agent：

{% code title=">_ Bash" %}

```bash
task use
```

{% endcode %}

**7. 启动 Web 服务器**

使用以下命令启动 Web 服务器：

{% code title=">_ Bash" %}

```bash
task run
```

{% endcode %}

**8. 编辑 Playground 设置**

在 [localhost:3000](http://localhost:3000) 打开 Playground 来配置你的 agent。
 1. 选择一个图类型 (例如，语音 Agent，实时 Agent)
 2. 选择一个对应的模块
 3. 选择一个扩展并配置其 API 密钥设置

![模块示例](https://github.com/TEN-framework/docs/blob/main/assets/gif/module-example.gif?raw=true)