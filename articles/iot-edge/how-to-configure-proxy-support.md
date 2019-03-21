---
title: 为设备配置网络代理 - Azure IoT Edge | Microsoft Docs
description: 如何将 Azure IoT Edge 运行时和所有面向 Internet 的 IoT Edge 模块配置为通过代理服务器进行通信。
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 33f5cd6e1d2989a9ca5c26bbcf947bd6eade3831
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57774194"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>将 IoT Edge 设备配置为通过代理服务器进行通信

IoT Edge 设备将发送 HTTPS 请求以与 IoT 中心进行通信。 如果设备已连接到使用代理服务器的网络，则需要将 IoT Edge 运行时配置为通过该服务器进行通信。 如果代理服务器发出不通过 IoT Edge 中心路由的 HTTP 或 HTTPS 请求，则可能还会影响各个 IoT Edge 模块。 

将 IoT Edge 设备配置为使用代理服务器的基本步骤如下： 

1. 在设备上安装 IoT Edge 运行时。 
2. 将设备上的 Docker 守护程序和 IoT Edge 守护程序配置为使用代理服务器。
3. 配置设备上 config.yaml 文件中的 edgeAgent 属性。
4. 为部署清单中的 IoT Edge 运行时和其他 IoT Edge 模块设置环境变量。

## <a name="know-your-proxy-url"></a>知道你的代理 URL

若要同时在设备上配置 Docker 守护程序和 IoT Edge，需要知道你的代理 URL。

代理 URL 采用以下格式：**protocol**://**proxy_host**:**proxy_port**。

* **protocol** 是 HTTP 或 HTTPS。 Docker 守护程序可以根据容器注册表设置使用任一协议，但 IoT Edge 守护程序和运行时容器应当始终使用 HTTPS。

* **proxy_host** 是代理服务器的地址。 如果你的代理服务器要求进行身份验证，则可以采用 **user**:**password**\@**proxy_host** 格式将你的凭据提供为 proxy_host 的一部分。

* **proxy_port** 是代理用来响应网络流量的网络端口。

## <a name="install-the-runtime"></a>安装运行时

若要在 Linux 设备上安装 IoT Edge 运行时，请将包管理器配置为通过代理服务器访问安装包。 例如，[设置 apt-get 以使用 http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy)。 配置包管理器后，请按照[在 Linux (ARM32v7/armhf) 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux-arm.md)或[在 Linux (x64) 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux.md)中的说明照常进行操作。

如果要在 Windows 设备上安装 IoT Edge 运行时，则需要通过代理服务器一次以下载安装程序脚本文件，然后在安装期间再通过代理服务器一次以下载必要的组件。 可以在 Windows 设置中配置代理信息，或直接在安装脚本中包含代理信息。 以下 powershell 脚本是使用 `-proxy` 参数安装 Windows 的示例：

```powershell
. {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -proxy <proxy URL>
```

如果拥有不能包含在 URL 中的复杂代理服务器凭据，请使用 `-InvokeWebRequestParameters` 中的 `-ProxyCredential` 参数。 例如，

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

有关代理参数的详细信息，请参阅 [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)。 有关安装选项的详细信息，请参阅[在 Windows 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-windows.md)。

安装 IoT Edge 运行时后，请使用以下部分为其配置代理信息。 

## <a name="configure-the-daemons"></a>配置守护程序

需要将 IoT Edge 设备上运行的 Docker 和 IoT Edge 守护程序配置为使用代理服务器。 Docker 守护程序发出 Web 请求，以从容器注册表中拉取容器映像。 IoT Edge 守护程序发出 Web 请求，以与 IoT 中心进行通信。

### <a name="docker-daemon"></a>Docker 守护程序

要使用环境变量配置 Docker 守护程序，请参阅 Docker 文档。 大多数容器注册表（包括 DockerHub 和 Azure 容器注册表）支持 HTTPS 请求，因此，你应当设置的变量为 **HTTPS_PROXY**。 如果要从不支持传输层安全性 (TLS) 的注册表中拉取映像，则应当设置 **HTTP_PROXY**。 

选择适用于 Docker 版本的项目： 

* [适用于 Linux 的 Docker](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [适用于 Windows 的 Docker](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)

### <a name="iot-edge-daemon"></a>IoT Edge 守护程序

IoT Edge 守护程序以类似的方式配置为 Docker 守护程序。 IoT Edge 发送到 IoT 中心的所有请求都使用 HTTPS。 使用以下步骤根据所使用的操作系统为服务设置环境变量。 

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

打开 IoT Edge 设备上的 config.yaml 文件。 在 Linux 系统上，此文件位于 /etc/iotedge/config.yaml。 在 Windows 系统上，此文件位于 C:\ProgramData\iotedge\config.yaml。 配置文件是受保护的，因此，你需要管理权限才能对其进行访问。 在 Linux 系统上，这意味着要先使用 `sudo` 命令，然后再在首选文本编辑器中打开该文件。 在 Windows 上，这意味着要以管理员身份打开文本编辑器（如记事本），然后打开该文件。 

在 config.yaml 文件中，找到“Edge 代理模块规范”部分。 IoT Edge 代理定义包括可以在其中添加环境变量的 **env** 参数。 

<!--
![edgeAgent definition](./media/how-to-configure-proxy-support/edgeagent-unedited.png)
-->

删除作为 env 参数占位符的大括号，并在新行上添加新变量。 请记住，YAML 中的缩进为两个空格。 

```yaml
https_proxy: "<proxy URL>"
```

默认情况下，IoT Edge 运行时使用 AMQP 与 IoT 中心通信。 某些代理服务器会阻止 AMQP 端口。 如果是这种情况，则还需要将 edgeAgent 配置为使用基于 WebSocket 的 AMQP。 添加第二个环境变量。

```yaml
UpstreamProtocol: "AmqpWs"
```

![具有环境变量的 edgeAgent 定义](./media/how-to-configure-proxy-support/edgeagent-edited.png)

将更改保存到 config.yaml 并关闭编辑器。 重新启动 IoT Edge 以使更改生效。 

* Linux： 

   ```bash
   sudo systemctl restart iotedge
   ```

* Windows:

   ```powershell
   Restart-Service iotedge
   ```

## <a name="configure-deployment-manifests"></a>配置部署清单  

将 IoT Edge 设备配置为与代理服务器配合使用后，还需要在将来的部署清单中声明环境变量。 始终配置两个运行时模块（edgeAgent 和 edgeHub），以通过代理服务器进行通信，从而维持与 IoT 中心的连接。 应该为代理服务器配置连接到 Internet 的其他 IoT Edge 模块。 但是，通过 edgeHub 路由其消息的模块或仅与设备上的其他模块通信的模块不需要代理服务器详细信息。 

可以使用 Azure 门户创建部署清单或通过编辑 JSON 文件来手动创建部署清单。 

### <a name="azure-portal"></a>Azure 门户

使用“设置模块”向导为 IoT Edge 设备创建部署时，每个模块都有可用于配置代理服务器连接的“环境变量”部分。 

若要配置 IoT Edge 代理和 IoT Edge 中心模块，请在向导的第一步中选择“配置 Edge 运行时高级设置”。 

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
            "value": "https://proxy.example.com:3128"
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

