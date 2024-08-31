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

# Customize your agent

There are two primary methods to customize the Astra agent locally:

## Using the power of graph designer (recommended)

<figure><img src="../assets/gif/graph_designer.gif" alt=""><figcaption></figcaption></figure>

The Graph Designer is a user-friendly, visual tool that allows you to create and modify the behavior and responses of the Astra agent without needing to write code. This approach is highly recommended for its ease of use and efficiency. By leveraging the Graph Designer, you can quickly design complex interactions and workflows through a graphical interface, making it accessible even for those with limited programming experience.

## Editing the code yourself

For those who prefer a more hands-on approach or need advanced customization, you can directly edit the source code of the Astra agent. This method requires a good understanding of the codebase and programming languages used in the project. By manually editing the code, you have full control over every aspect of the agent’s functionality, allowing for highly tailored and specific customizations. This approach is ideal for developers who need to implement unique features or integrate the agent with other systems.

{% tabs %}
{% tab title="property.json" %}
<pre class="language-json" data-title="./agents/property.json"><code class="lang-json">// ...
"nodes": [
    {
        "type": "extension",
        "extension_group": "default",
        "addon": "agora_rtc",
        "name": "agora_rtc",
        "property": {
<strong>            "app_id": "app-id",
</strong>            "token": "&#x3C;agora_token>",
            "channel": "astra_agents_test",
            "stream_id": 1234,
            "remote_stream_id": 123,
            "subscribe_audio": true,
            "publish_audio": true,
            "publish_data": true,
            "enable_agora_asr": true,
            "agora_asr_vendor_name": "microsoft",
            "agora_asr_language": "en-US",
<strong>            "agora_asr_vendor_key": "stt-key",
</strong><strong>            "agora_asr_vendor_region": "stt-region",
</strong>            "agora_asr_session_control_file_path": "session_control.conf"
        }
    },
// ...
</code></pre>

{% endtab %}

{% tab title="config.go" %}
<pre class="language-go" data-title="config.go"><code class="lang-go">// ...
// Default graph name
<strong>graphNameDefault = "va.openai.azure"
</strong>// ...
</code></pre>
{% endtab %}
{% endtabs %}

Save both files, then run `make build`. Your agent, complete with the intended extensions, should now be up and running.
