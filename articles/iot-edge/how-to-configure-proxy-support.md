---
title: 为网络代理配置 Azure IoT Edge 设备 | Microsoft 文档
description: 如何将 Azure IoT Edge 运行时和所有面向 Internet 的 IoT Edge 模块配置为通过代理服务器进行通信。
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cf8f6197c65b0169e2bc61f46ab4a22f212512a6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996717"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>将 IoT Edge 设备配置为通过代理服务器进行通信

IoT Edge 设备将发送 HTTPS 请求以与 IoT 中心进行通信。 如果设备已连接到使用代理服务器的网络，则需要将 IoT Edge 运行时配置为通过该服务器进行通信。 如果代理服务器发出不通过 Edge 中心路由的 HTTP 或 HTTPS 请求，则可能还会影响各个 IoT Edge 模块。 

将 IoT Edge 设备配置为使用代理服务器的基本步骤如下： 

1. 将设备上的 Docker 守护程序和 IoT Edge 守护程序配置为使用代理服务器。
2. 配置设备上 config.yaml 文件中的 edgeAgent 属性。
3. 为部署清单中的 IoT Edge 运行时和其他 IoT Edge 模块设置环境变量。 

## <a name="configure-the-daemons"></a>配置守护程序

需要将 IoT Edge 设备上运行的 Docker 和 IoT Edge 守护程序配置为使用代理服务器。 Docker 守护程序发出 Web 请求，以从容器注册表中拉取容器映像。 IoT Edge 守护程序发出 Web 请求，以与 IoT 中心进行通信。

### <a name="docker-daemon"></a>Docker 守护程序

要使用环境变量配置 Docker 守护程序，请参阅 Docker 文档。 大多数容器注册表（包括 DockerHub 和 Azure 容器注册表）支持 HTTPS 请求，因此，应设置的变量为 HTTPS_PROXY。 如果要从不支持传输层安全性 (TLS) 的注册表中拉取映像，则可能应设置 HTTP_PROXY。 

选择适用于 Docker 版本的项目： 

* [Docker](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [适用于 Windows 的 Docker](https://docs.docker.com/docker-for-windows/#proxies)

### <a name="iot-edge-daemon"></a>IoT Edge 守护程序

IoT Edge 守护程序以类似的方式配置为 Docker 守护程序。 IoT Edge 发送到 IoT 中心的所有请求都使用 HTTPS。 使用以下步骤根据所使用的操作系统为服务设置环境变量。 

#### <a name="linux"></a>Linux

在终端中打开编辑器以配置 IoT Edge 守护程序。 

```bash
sudo systemctl edit iotedge
```

输入以下文本，将 **\<代理 URL>** 替换为代理服务器地址和端口。 然后，保存并退出。 

```text
[Service]
Environment="https_proxy=<proxy URL>"
```

刷新服务管理器以选取 iotedge 的新配置。

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

## <a name="configure-the-edge-agent"></a>配置 Edge 代理

Edge 代理是在任意 IoT Edge 设备上启动的第一个模块。 该代理基于 IoT Edge config.yaml 文件中的信息首次启动， 然后连接到 IoT 中心，以检索部署清单，其中声明了应在设备上部署其他哪些模块。

打开 IoT Edge 设备上的 config.yaml 文件。 在 Linux 系统上，此文件位于 /etc/iotedge/config.yaml。 在 Windows 系统上，此文件位于 C:\ProgramData\iotedge\config.yaml。 配置文件是受保护的，因此，你需要管理权限才能对其进行访问。 在 Linux 系统上，这意味着要先使用 `sudo` 命令，然后再在首选文本编辑器中打开该文件。 在 Windows 上，这意味着要以管理员身份打开文本编辑器（如记事本），然后打开该文件。 

在 config.yaml 文件中，找到“Edge 代理模块规范”部分。 Edge 代理定义包括可以在其中添加环境变量的 env 参数。 

![edgeAgent 定义](./media/how-to-configure-proxy-support/edgeagent-unedited.png)

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

将 IoT Edge 设备配置为使用代理服务器后，还需要在所有将来的部署清单中声明环境变量。 两个运行时模块（edgeAgent 和 edgeHub）应始终将代理服务器配置为与 IoT 中心保持通信。 可以将任何 IoT Edge 模块配置为通过代理服务器进行通信，但对于通过 edgeHub 路由其消息的模块或仅与设备上的其他模块进行通信的模块来说，不需要如此配置。 

可以使用 Azure 门户创建部署清单或通过编辑 JSON 文件来手动创建部署清单。 

### <a name="azure-portal"></a>Azure 门户

使用“设置模块”向导为 IoT Edge 设备创建部署时，每个模块都有可用于配置代理服务器连接的“环境变量”部分。 

若要配置 Edge 代理和 Edge 中心模块，请在向导的第一步中选择“配置 Edge 运行时高级设置”。 

![配置 Edge 运行时高级设置](./media/how-to-configure-proxy-support/configure-runtime.png)

将 https_proxy 环境变量添加到 Edge 代理和 Edge 中心模块定义。 如果在 IoT Edge 设备的 config.yaml 文件中包括 UpstreamProtocol 环境变量，也请将其添加到 Edge 代理模块定义。 

![设置环境变量。](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

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

如果在 IoT Edge 设备的 confige.yaml 文件中包括 UpstreamProtocol 环境变量，也请将其添加到 Edge 代理模块定义。 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>后续步骤

了解有关 [IoT Edge 运行时](iot-edge-runtime.md)的角色详细信息。

使用 [Azure IoT Edge 的常见问题和解决方法](troubleshoot.md)解决安装和配置错误

