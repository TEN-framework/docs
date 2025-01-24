# 语音助手

本指南将帮助您在 TEN-Agent Playground 中使用大语言模型运行语音助手。

## STT + TTS + LLM

### 前提条件

- 确保您已运行 TEN-Agent Playground。如果没有，请按照[运行 Playground](https://doc.theten.ai/ten-agent/quickstart) 指南启动 Playground。
- 您需要准备以下信息：
  - STT 信息，可以使用任何支持的 STT。[Deepgram](https://deepgram.com/) 相对容易注册和上手。
  - TTS 信息，可以使用任何支持的 TTS。[Fish.Audio](https://fish.audio/) 相对容易注册和上手。
  - LLM 信息，可以使用任何支持的 LLM。建议使用 [OpenAI](https://openai.com)。
  - RTC 信息，目前仅支持 Agora RTC。您可以在 [Agora](https://www.agora.io/) 注册您的帐户。我们假设您在配置 `.env` 文件时已准备好您的 App ID 和 App Certificate。

### 步骤

1. 打开 [localhost:3000](http://localhost:3000) 上的 Playground 以配置您的智能体。
2. 选择图表类型 `voice_assistant`。
3. 单击“模块选择器”以打开模块选择。
4. 如果您首选的 STT/TTS 模块默认未选中，您可以从下拉列表中选择模块。请注意，您需要使用 API 密钥等正确信息配置模块。
5. 从“LLM”模块中，单击下拉列表并选择您首选的大型语言模型。
6. 单击“保存更改”以将模块应用于图表。
7. 单击图表选择右侧的按钮以打开属性配置。您将看到可以为选定的大型语言模型配置的属性列表。
8. 使用您准备的信息配置属性。
9. 单击“保存更改”以将属性应用于大型语言模型。
10. 如果您看到成功提示，则表示属性已成功应用于大型语言模型。
11. 您已全部设置完毕！现在，您可以单击“连接”按钮开始与语音助手对话。请注意，您需要等待几秒钟才能初始化智能体。

### 使用 Azure STT

Azure STT 集成在 RTC 扩展模块中。因此，如果您想使用 Azure STT，您需要选择 `voice_assistant_integrated_stt` 图表类型。

### 将天气工具绑定到您的 LLM

您可以在 TEN-Agent Playground 中将天气工具绑定到您的 LLM 模块。
建议使用以下 OpenAI LLM。

1. 当您的智能体运行时。打开模块选择器。
2. 单击 LLM 模块右侧的按钮以打开工具选择。
3. 从弹出列表中选择“天气工具”。
4. 单击“保存更改”以将工具应用于 LLM 模块。
5. 如果您看到成功提示，则表示该工具已成功应用于 LLM 模块。
6. 您已全部设置完毕！现在，您可以通过与智能体对话来询问天气。

## 实时对话 V2V

### 前提条件

- 确保您已运行 TEN-Agent Playground。如果没有，请按照[运行 Playground](https://doc.theten.ai/ten-agent/quickstart) 指南启动 Playground。
- 您需要准备以下信息：
  - 实时 API 密钥
- RTC 信息，目前仅支持 Agora RTC。您可以在 [Agora](https://www.agora.io/) 注册您的帐户。我们假设您在配置 `.env` 文件时已准备好您的 App ID 和 App Certificate。

### 步骤

1. 打开 [localhost:3000](http://localhost:3000) 上的 Playground 以配置您的智能体。
2. 选择图表类型 `voice_assistant_realtime`。
3. 单击“模块选择器”以打开模块选择。
4. 从下拉列表中选择您首选的 V2V 模块。
5. 单击“保存更改”以将模块应用于图表。
6. 单击图表选择右侧的按钮以打开属性配置。您将看到可以为选定的 V2V 模块配置的属性列表。
7. 使用您准备的信息配置“实时 API 密钥”属性。
8. 单击“保存更改”以将属性应用于 V2V 模块。
9. 如果您看到成功提示，则表示该属性已成功应用于 V2V 模块。
10. 您已全部设置完毕！现在，您可以单击“连接”按钮开始与语音助手对话。请注意，您需要等待几秒钟才能初始化智能体。

### 将天气工具绑定到您的 V2V

您可以在 TEN-Agent Playground 中将天气工具绑定到您的 V2V 模块。

1. 当您的智能体运行时。打开模块选择器。
2. 单击 V2V 模块右侧的按钮以打开工具选择。
3. 从弹出列表中选择“天气工具”。
4. 单击“保存更改”以将工具应用于 V2V 模块。
5. 如果您看到成功提示，则表示该工具已成功应用于 V2V 模块。
6. 您已全部设置完毕！现在，您可以通过与智能体对话来询问天气。
