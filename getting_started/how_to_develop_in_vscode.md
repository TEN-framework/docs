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

Since the compilation and development of TEN are generally recommended to be done within a container, when using VSCode for development outside the container, you might encounter issues where symbols cannot be resolved. This issue arises because some environment dependencies are installed within the container, and VSCode cannot recognize the environment inside the container, leading to the failure to resolve header files.
To address this, we need to mount VSCode within the container so that it can recognize the container’s environment and resolve the header files accordingly. We will use VSCode's Dev Containers and Docker extensions to achieve this.

## 1. Install the Docker Extension
First, we need to install the [Docker extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker), which allows us to manage Docker containers directly within VSCode.

## 2. Install the Dev Containers Extension
Next, we need to install the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers), which enables us to connect VSCode to Docker containers.

## 3. Start the Development Environment Using Docker Compose
This step is essentially the same as in the [Quick Start](https://doc.theten.ai/getting-started/quickstart), with the only difference being that instead of running:

```shell
docker compose up
```
We run:
```shell
docker compose up -d
```

After executing this command, you will see that the container has started. Open VSCode, switch to the Docker extension, and you should see the running container.
![Docker Containers](./images/docker_containers.png)

## 4. Connect to the Container
In the Docker extension within VSCode, find the `astra_agents_dev` container in the list, and click `Attach Visual Studio Code` to connect to the container. VSCode will then open a new window that is connected to the container, where you can proceed with development.

{% hint style="info" %}
In the Dev Container environment connected to the container, your local extensions and settings will not be applied, since this environment is within the container. Therefore, you will need to install extensions and configure settings inside the container. To install extensions within the container, simply open the newly launched VSCode window, click on Extensions on the left sidebar, search for the required extension, and follow the prompts to install it inside the container.
{% endhint %}