---
title: 为设备配置网络代理 - Azure IoT Edge | Microsoft Docs
description: 如何将 Azure IoT Edge 运行时和所有面向 Internet 的 IoT Edge 模块配置为通过代理服务器进行通信。
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 34a94a1b9c38070f5c9de22d9a9e4f24183a876c
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151001"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>将 IoT Edge 设备配置为通过代理服务器进行通信

IoT Edge 设备将发送 HTTPS 请求以与 IoT 中心进行通信。 如果设备已连接到使用代理服务器的网络，则需要将 IoT Edge 运行时配置为通过该服务器进行通信。 如果代理服务器发出不通过 IoT Edge 中心路由的 HTTP 或 HTTPS 请求，则可能还会影响各个 IoT Edge 模块。 

本文将指导完成配置并管理代理服务器后面的 IoT Edge 设备的以下四个步骤： 

1. **在设备上安装 IoT Edge 运行时。**

   因此，你的设备需要通过代理服务器发出这些请求进行通信，IoT Edge 安装脚本会从 internet，拉取包和文件。 有关详细步骤，请参阅[安装通过代理运行时](#install-the-runtime-through-a-proxy)本文的部分。 对于 Windows 设备，还提供安装脚本[脱机安装](how-to-install-iot-edge-windows.md#offline-installation)选项。 

   此步骤是首次设置它时，IoT Edge 设备上执行一次性的过程。 相同的连接也是必需的更新 IoT Edge 运行时。 

2. **在设备上配置 Docker 守护程序和 IoT Edge 守护程序。**

   IoT Edge 设备，这两个需要发出 web 请求通过代理服务器上使用两个守护程序。 IoT Edge 守护程序负责与 IoT 中心之间的通信。 小鲸鱼守护程序负责容器管理，因此与容器注册表进行通信。 有关详细步骤，请参阅[配置守护程序](#configure-the-daemons)本文的部分。 

   此步骤是首次设置它时，IoT Edge 设备上执行一次性的过程。

3. **你的设备上的 config.yaml 文件中配置 IoT Edge 代理属性。**

   IoT Edge 后台程序启动 edgeAgent 模块最初，但然后 edgeAgent 模块负责从 IoT 中心检索部署清单和启动所有其他模块。 若要建立初始连接到 IoT 中心的 IoT Edge 代理，edgeAgent 模块环境变量上手动配置设备本身。 在初始连接后可以远程配置 edgeAgent 模块。 有关详细步骤，请参阅[配置 IoT Edge 代理](#configure-the-iot-edge-agent)本文的部分。

   此步骤是首次设置它时，IoT Edge 设备上执行一次性的过程。

4. **将来的模块的所有部署，将都设置为通过代理进行通信的任何模块的环境变量。**

   一旦 IoT Edge 设备已设置，并通过代理服务器连接到 IoT 中心，您需要保持在将来的模块的所有部署中的连接。 有关详细步骤，请参阅[配置部署清单](#configure-deployment-manifests)本文的部分。 

   此步骤是一个持续的过程远程执行，以便每个新的模块或部署更新维护设备的功能，通过代理服务器进行通信。 

## <a name="know-your-proxy-url"></a>知道你的代理 URL

在开始任何这篇文章中的步骤之前，需要知道你的代理 URL。

代理 URL 采用以下格式：**protocol**://**proxy_host**:**proxy_port**。

* **protocol** 是 HTTP 或 HTTPS。 Docker 守护程序可以使用任一协议，具体取决于容器注册表设置，但 IoT Edge 守护程序和运行时容器应始终使用 HTTP 来连接到代理。

* **proxy_host** 是代理服务器的地址。 如果代理服务器要求进行身份验证，则可采用 **user**:**password**\@**proxy_host** 格式将凭据作为 proxy_host 的一部分提供。

* **proxy_port** 是代理用来响应网络流量的网络端口。

## <a name="install-the-runtime-through-a-proxy"></a>安装通过代理运行时

无论在 Windows 或 Linux 上运行你的 IoT Edge 设备，需要通过代理服务器访问安装包。 具体取决于您的操作系统，请按照步骤安装 IoT Edge 运行时通过代理服务器。 

### <a name="linux"></a>Linux

若要在 Linux 设备上安装 IoT Edge 运行时，请将包管理器配置为通过代理服务器访问安装包。 例如，[设置 apt-get 以使用 http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy)。 配置包管理器后，请按照[在 Linux (ARM32v7/armhf) 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux-arm.md)或[在 Linux (x64) 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux.md)中的说明照常进行操作。

### <a name="windows"></a>Windows

若要在 Windows 设备上安装 IoT Edge 运行时，需要两次通过代理服务器执行操作。 第一个连接下载安装程序脚本文件，并且第二个连接以下载必要的组件，在安装过程。 可以在 Windows 设置中配置代理信息，或直接在 PowerShell 命令中包含代理信息。 

以下步骤演示使用 `-proxy` 参数安装 Windows 的示例：

1. Invoke-WebRequest 命令需要获得代理信息才能访问安装程序脚本。 然后，Deploy-IoTEdge 命令需要获得代理信息才能下载安装文件。 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Initialize-IoTEdge 命令不需经过代理服务器，因此第二步仅需 Invoke-WebRequest 的代理信息。

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge

If you have complicated credentials for the proxy server that can't be included in the URL, use the `-ProxyCredential` parameter within `-InvokeWebRequestParameters`. For example,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

有关代理参数的详细信息，请参阅 [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)。 有关 Windows 安装选项，包括脱机安装，请参阅[在 Windows 上的安装 Azure IoT Edge 运行时](how-to-install-iot-edge-windows.md)。

## <a name="configure-the-daemons"></a>配置守护程序

IoT Edge 在 IoT Edge 设备上运行的两个守护程序上依赖于。 Moby 守护程序发出 Web 请求，以从容器注册表中拉取容器映像。 IoT Edge 守护程序发出 Web 请求，以与 IoT 中心进行通信。

小鲸鱼和 IoT Edge 守护程序必须配置为使用代理服务器进行持续的设备功能。 此步骤需要初次设置设备在 IoT Edge 设备上的发生。 

### <a name="moby-daemon"></a>Moby 守护程序

由于 Moby 是基于 Docker 的，因此若要使用环境变量配置 Moby 守护程序，请参阅 Docker 文档。 大多数容器注册表（包括 DockerHub 和 Azure 容器注册表）支持 HTTPS 请求，因此，你应当设置的变量为 **HTTPS_PROXY**。 如果要从不支持传输层安全性 (TLS) 的注册表中拉取映像，则应当设置 **HTTP_PROXY**。 

选择适用于 IoT Edge 设备操作系统的文章： 

* [在 Linux 上配置 Docker 守护程序](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * Linux 设备上的 Moby 守护程序保留“Docker”这一名称。
* [在 Windows 上配置 Docker 守护程序](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * Windows 设备上的 Moby 守护程序称为 iotedge-moby。 之所以让这些名称保持不同，是因为可能会在 Windows 设备上并行运行 Docker Desktop 和 Moby。 

### <a name="iot-edge-daemon"></a>IoT Edge 守护程序

IoT Edge 守护程序以类似的方式配置为 Moby 守护程序。 使用以下步骤根据所使用的操作系统为服务设置环境变量。 

IoT Edge 守护程序始终使用 HTTPS 将请求发送到 IoT 中心。

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

此步骤需要初次设置设备的 IoT Edge 设备上一次。 

1. 打开 IoT Edge 设备上的 config.yaml 文件。 在 Linux 系统上，此文件位于 /etc/iotedge/config.yaml  。 在 Windows 系统上，此文件位于 C:\ProgramData\iotedge\config.yaml  。 配置文件是受保护的，因此，你需要管理权限才能对其进行访问。 在 Linux 系统上使用`sudo`命令之前在首选的文本编辑器中打开该文件。 在 Windows 中，打开记事本之类的文本编辑器，以管理员身份，然后打开文件。 

2. 在 config.yaml 文件中，找到“Edge 代理模块规范”  部分。 IoT Edge 代理定义包括可以在其中添加环境变量的 **env** 参数。 

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

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>配置部署清单  

将 IoT Edge 设备配置为与代理服务器配合使用后，还需要在将来的部署清单中声明环境变量。 可以编辑部署清单进行签名，使用 Azure 门户向导，也可以通过编辑部署清单 JSON 文件。 

始终配置两个运行时模块（edgeAgent 和 edgeHub），以通过代理服务器进行通信，从而维持与 IoT 中心的连接。 如果从 edgeAgent 模块中删除的代理信息，请重新建立连接的唯一方法是通过编辑 config.yaml 文件在设备上，在上一部分中所述。 

连接到 internet 其他 IoT Edge 模块应配置为也通过代理服务器进行通信。 但是，通过 edgeHub 路由其消息的模块或仅与设备上的其他模块通信的模块不需要代理服务器详细信息。 

此步骤中正在进行的 IoT Edge 设备的整个生存期中。 

### <a name="azure-portal"></a>Azure 门户

使用“设置模块”  向导为 IoT Edge 设备创建部署时，每个模块都有可用于配置代理服务器连接的“环境变量”  部分。 

若要配置 IoT Edge 代理和 IoT Edge 中心模块，请在向导的第一步中选择“配置 Edge 运行时高级设置”  。 

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

