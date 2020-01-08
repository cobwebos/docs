---
title: 为设备配置网络代理 - Azure IoT Edge | Microsoft Docs
description: 如何将 Azure IoT Edge 运行时和所有面向 Internet 的 IoT Edge 模块配置为通过代理服务器进行通信。
author: kgremban
ms.author: kgremban
ms.date: 11/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6cc37875b84e215066c52ca8daef0fa0d879c54f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434464"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>将 IoT Edge 设备配置为通过代理服务器进行通信

IoT Edge 设备将发送 HTTPS 请求以与 IoT 中心进行通信。 如果设备已连接到使用代理服务器的网络，则需要将 IoT Edge 运行时配置为通过该服务器进行通信。 如果代理服务器发出不通过 IoT Edge 中心路由的 HTTP 或 HTTPS 请求，则可能还会影响各个 IoT Edge 模块。

本文逐步讲解以下四个步骤，配置并管理代理服务器后面的 IoT Edge 设备：

1. **在设备上安装 IoT Edge 运行时。**

   IoT Edge 安装脚本从 internet 提取包和文件，因此你的设备需要通过代理服务器进行通信以发出这些请求。 有关详细步骤，请参阅本文的[通过代理安装运行时](#install-the-runtime-through-a-proxy)部分。 对于 Windows 设备，安装脚本还提供[脱机安装](how-to-install-iot-edge-windows.md#offline-installation)选项。

   此步骤是第一次设置时在 IoT Edge 设备上执行的一次性过程。 更新 IoT Edge 运行时也需要相同的连接。

2. **在设备上配置 Docker 守护程序和 IoT Edge 守护程序。**

   IoT Edge 在设备上使用两个守护程序，这两者都需要通过代理服务器进行 web 请求。 IoT Edge 守护程序负责与 IoT 中心进行通信。 小鲸鱼后台程序负责容器管理，因此与容器注册表通信。 有关详细步骤，请参阅本文的[配置守护](#configure-the-daemons)程序部分。

   此步骤是第一次设置时在 IoT Edge 设备上执行的一次性过程。

3. **在设备上的 yaml 文件中配置 IoT Edge 代理属性。**

   IoT Edge daemon 最初会启动 edgeAgent 模块，但 edgeAgent 模块负责从 IoT 中心检索部署清单并启动所有其他模块。 要使 IoT Edge 代理初始连接到 IoT 中心，请在设备本身上手动配置 edgeAgent 模块环境变量。 初次连接后，你可以远程配置 edgeAgent 模块。 有关详细步骤，请参阅本文的[配置 IoT Edge 代理](#configure-the-iot-edge-agent)部分。

   此步骤是第一次设置时在 IoT Edge 设备上执行的一次性过程。

4. **对于所有将来的模块部署，为通过代理进行通信的任何模块设置环境变量。**

   设置 IoT Edge 设备并通过代理服务器连接到 IoT 中心后，需要在将来的所有模块部署中维护连接。 有关详细步骤，请参阅本文的[配置部署清单](#configure-deployment-manifests)部分。 

   此步骤是远程执行的进程，以便每个新的模块或部署更新保持设备通过代理服务器进行通信的能力。

## <a name="know-your-proxy-url"></a>知道你的代理 URL

在开始本文中的任何步骤之前，你需要了解你的代理 URL。

代理 URL 采用以下格式：**protocol**://**proxy_host**:**proxy_port**。

* **protocol** 是 HTTP 或 HTTPS。 Docker 后台程序可以使用任何一种协议，具体取决于容器注册表设置，但 IoT Edge 守护程序和运行时容器应始终使用 HTTP 连接到代理。

* **proxy_host** 是代理服务器的地址。 如果代理服务器要求身份验证，则可以提供凭据作为代理主机的一部分，格式如下： **user**：**password**\@**proxy_host**。

* **proxy_port** 是代理用来响应网络流量的网络端口。

## <a name="install-the-runtime-through-a-proxy"></a>通过代理安装运行时

无论 IoT Edge 设备是在 Windows 还是 Linux 上运行，都需要通过代理服务器来访问安装包。 根据你的操作系统，请遵循通过代理服务器安装 IoT Edge 运行时的步骤。

### <a name="linux-devices"></a>Linux 设备

若要在 Linux 设备上安装 IoT Edge 运行时，请将包管理器配置为通过代理服务器访问安装包。 例如，[设置 apt-get 以使用 http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy)。 配置程序包管理器后，请按照通常情况下在[Linux 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux.md)中的说明进行操作。

### <a name="windows-devices"></a>Windows 设备

如果要在 Windows 设备上安装 IoT Edge 运行时，则需要通过代理服务器两次。 第一次连接将下载安装程序脚本文件，而第二次连接是在安装过程中下载所需的组件。 可以在 Windows 设置中配置代理信息，或直接在 PowerShell 命令中包含代理信息。

以下步骤演示使用 `-proxy` 参数的 windows 安装的示例：

1. WebRequest 命令需要使用代理信息来访问安装程序脚本。 然后，IoTEdge 命令需要代理信息来下载安装文件。 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. IoTEdge 命令无需通过代理服务器，因此第二个步骤只需要 WebRequest 的代理信息。

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

如果拥有不能包含在 URL 中的复杂代理服务器凭据，请使用 `-InvokeWebRequestParameters` 中的 `-ProxyCredential` 参数。 例如，

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

有关代理参数的详细信息，请参阅 [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)。 有关 Windows 安装选项（包括脱机安装）的详细信息，请参阅[在 windows 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-windows.md)。

## <a name="configure-the-daemons"></a>配置守护程序

IoT Edge 依赖于 IoT Edge 设备上运行的两个守护程序。 小鲸鱼后台程序使 web 请求从容器注册表拉取容器映像。 IoT Edge 守护程序发出 Web 请求，以与 IoT 中心进行通信。

需要将小鲸鱼和 IoT Edge 守护程序配置为使用代理服务器进行持续的设备功能。 在初始设备安装过程中，此步骤发生在 IoT Edge 设备上。

### <a name="moby-daemon"></a>小鲸鱼后台程序

由于小鲸鱼是在 Docker 上构建的，因此请参阅 Docker 文档，用环境变量配置小鲸鱼守护程序。 大多数容器注册表（包括 DockerHub 和 Azure 容器注册表）支持 HTTPS 请求，因此，你应当设置的变量为 **HTTPS_PROXY**。 如果要从不支持传输层安全性 (TLS) 的注册表中拉取映像，则应当设置 **HTTP_PROXY**。

选择适用于你的 IoT Edge 设备操作系统的文章：

* [在 Linux 上配置 Docker 守护](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)程序Linux 设备上的小鲸鱼后台程序保留名称 Docker。
* [在 Windows 上配置 Docker 守护](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)程序Windows 设备上的小鲸鱼后台程序称为 iotedge-小鲸鱼。 名称不同，因为可以在 Windows 设备上并行运行 Docker Desktop 和小鲸鱼。

### <a name="iot-edge-daemon"></a>IoT Edge 守护程序

IoT Edge 守护程序的配置方式与小鲸鱼守护程序类似。 使用以下步骤根据所使用的操作系统为服务设置环境变量。

IoT Edge daemon 始终使用 HTTPS 向 IoT 中心发送请求。

#### <a name="linux"></a>Linux

在终端中打开编辑器以配置 IoT Edge 守护程序。

```bash
sudo systemctl edit iotedge
```

输入以下文本，将 **\<代理 URL>** 替换为代理服务器地址和端口。 然后，保存并退出。

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

刷新服务管理器以选取 IoT Edge 的新配置。

```bash
sudo systemctl daemon-reload
```

重新启动 IoT Edge 以使更改生效。

```bash
sudo systemctl restart iotedge
```

确认已创建环境变量并加载了新配置。

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

以管理员身份打开 PowerShell 窗口，运行以下命令来使用新的环境变量编辑注册表。 将 **\<代理 URL>** 替换为代理服务器地址和端口。

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

重新启动 IoT Edge 以使更改生效。

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>配置 IoT Edge 代理

IoT Edge 代理是在任意 IoT Edge 设备上启动的第一个模块。 该代理基于 IoT Edge config.yaml 文件中的信息首次启动， IoT Edge 代理随后连接到 IoT 中心以检索部署清单，其中声明了应在设备上部署的其他模块。

在初始设备安装过程中，此步骤只在 IoT Edge 设备上发生一次。 

1. 打开 IoT Edge 设备上的 config.yaml 文件。 在 Linux 系统上，此文件位于 /etc/iotedge/config.yaml。 在 Windows 系统上，此文件位于 C:\ProgramData\iotedge\config.yaml。 配置文件是受保护的，因此，你需要管理权限才能对其进行访问。 在 Linux 系统上，使用 `sudo` 命令，然后在首选文本编辑器中打开文件。 在 Windows 上，以管理员身份打开文本编辑器（如记事本），然后打开文件。 

2. 在 config.yaml 文件中，找到“Edge 代理模块规范”部分。 IoT Edge 代理定义包括可以在其中添加环境变量的 **env** 参数。 

3. 删除作为 env 参数占位符的大括号，并在新行上添加新变量。 请记住，YAML 中的缩进为两个空格。 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. 默认情况下，IoT Edge 运行时使用 AMQP 与 IoT 中心通信。 某些代理服务器会阻止 AMQP 端口。 如果是这种情况，则还需要将 edgeAgent 配置为使用基于 WebSocket 的 AMQP。 添加第二个环境变量。

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![具有环境变量的 edgeAgent 定义](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. 将更改保存到 config.yaml 并关闭编辑器。 重新启动 IoT Edge 以使更改生效。 

   * Linux：

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows：

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>配置部署清单  

将 IoT Edge 设备配置为与代理服务器配合使用后，还需要在将来的部署清单中声明环境变量。 您可以使用 Azure 门户向导或通过编辑部署清单 JSON 文件编辑部署清单。

始终配置两个运行时模块（edgeAgent 和 edgeHub），以通过代理服务器进行通信，从而维持与 IoT 中心的连接。 如果从 edgeAgent 模块中删除代理信息，重新建立连接的唯一方法是在设备上编辑 yaml 文件，如前一部分中所述。

还应将连接到 internet 的其他 IoT Edge 模块配置为通过代理服务器进行通信。 但是，通过 edgeHub 路由其消息的模块或仅与设备上的其他模块通信的模块不需要代理服务器详细信息。

此步骤贯穿 IoT Edge 设备的整个生命周期。

### <a name="azure-portal"></a>Azure 门户

使用“设置模块”向导为 IoT Edge 设备创建部署时，每个模块都有可用于配置代理服务器连接的“环境变量”部分。

若要配置 IoT Edge 代理和 IoT Edge 中心模块，请在向导的第一步中选择 "**运行时设置**"。

![配置 Edge 运行时高级设置](./media/how-to-configure-proxy-support/configure-runtime.png)

将 **https_proxy** 环境变量添加到 IoT Edge 代理和 IoT Edge 中心模块定义。 如果在 IoT Edge 设备的 config.yaml 文件中包括 **UpstreamProtocol** 环境变量，也请将其添加到 IoT Edge 代理模块定义。

![设置 https_proxy 环境变量](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

添加到部署清单的所有其他模块都遵循相同的模式。 在设置模块名称和映像的页面中，具有环境变量部分。

### <a name="json-deployment-manifest-files"></a>JSON 部署清单文件

如果使用 Visual Studio Code 中的模板或通过手动创建 JSON 文件来为 IoT Edge 设备创建部署，则可以将环境变量直接添加到每个模块定义。

使用以下 JSON 格式：

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

如果包括环境变量，模块定义应类似以下 edgeHub 示例：

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

如果在 IoT Edge 设备的 confige.yaml 文件中包括 **UpstreamProtocol** 环境变量，也请将其添加到 IoT Edge 代理模块定义。

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>后续步骤

了解有关 [IoT Edge 运行时](iot-edge-runtime.md)的角色详细信息。

使用 [Azure IoT Edge 的常见问题和解决方法](troubleshoot.md)解决安装和配置错误
