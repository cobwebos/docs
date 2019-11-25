---
title: 为设备配置网络代理 - Azure IoT Edge | Microsoft Docs
description: 如何将 Azure IoT Edge 运行时和所有面向 Internet 的 IoT Edge 模块配置为通过代理服务器进行通信。
author: kgremban
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9a4ed17ceddf01ec628d80dc3ba9f4d7ee305f3b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457151"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>将 IoT Edge 设备配置为通过代理服务器进行通信

IoT Edge 设备将发送 HTTPS 请求以与 IoT 中心进行通信。 如果设备已连接到使用代理服务器的网络，则需要将 IoT Edge 运行时配置为通过该服务器进行通信。 如果代理服务器发出不通过 IoT Edge 中心路由的 HTTP 或 HTTPS 请求，则可能还会影响各个 IoT Edge 模块。 

This article walks through the following four steps to configure and then manage an IoT Edge device behind a proxy server: 

1. **Install the IoT Edge runtime on your device.**

   The IoT Edge installation scripts pull packages and files from the internet, so your device needs to communicate through the proxy server to make those requests. For detailed steps, see the [Install the runtime through a proxy](#install-the-runtime-through-a-proxy) section of this article. For Windows devices, the installation script also provides an [Offline installation](how-to-install-iot-edge-windows.md#offline-installation) option. 

   This step is a one-time process performed on the IoT Edge device when you first set it up. The same connections are also required when you update the IoT Edge runtime. 

2. **Configure the Docker daemon and the IoT Edge daemon on your device.**

   IoT Edge uses two daemons on the device, both of which need to make web requests through the proxy server. The IoT Edge daemon is responsible for communications with IoT Hub. The Moby daemon is responsible for container management, so communicates with container registries. For detailed steps, see the [Configure the daemons](#configure-the-daemons) section of this article. 

   This step is a one-time process performed on the IoT Edge device when you first set it up.

3. **Configure the IoT Edge agent properties in the config.yaml file on your device.**

   The IoT Edge daemon starts the edgeAgent module initially, but then the edgeAgent module is responsible for retrieving the deployment manifest from IoT Hub and starting all the other modules. For the IoT Edge agent to make the initial connection to IoT Hub, configure the edgeAgent module environment variables manually on the device itself. After the initial connection, you can configure the edgeAgent module remotely. For detailed steps, see the [Configure the IoT Edge agent](#configure-the-iot-edge-agent) section of this article.

   This step is a one-time process performed on the IoT Edge device when you first set it up.

4. **For all future module deployments, set environment variables for any module communicating through the proxy.**

   Once your IoT Edge device is set up and connected to IoT Hub through the proxy server, you need to maintain the connection in all future module deployments. For detailed steps, see the [Configure deployment manifests](#configure-deployment-manifests) section of this article. 

   This step is an ongoing process performed remotely so that every new module or deployment update maintains the device's ability to communicate through the proxy server. 

## <a name="know-your-proxy-url"></a>知道你的代理 URL

Before you begin any of the steps in this article, you need to know your proxy URL.

代理 URL 采用以下格式：**protocol**://**proxy_host**:**proxy_port**。

* **protocol** 是 HTTP 或 HTTPS。 The Docker daemon can use either protocol, depending on your container registry settings, but the IoT Edge daemon and runtime containers should always use HTTP to connect to the proxy.

* **proxy_host** 是代理服务器的地址。 If your proxy server requires authentication, you can provide your credentials as part of the proxy host with the following format: **user**:**password**\@**proxy_host**.

* **proxy_port** 是代理用来响应网络流量的网络端口。

## <a name="install-the-runtime-through-a-proxy"></a>Install the runtime through a proxy

Whether your IoT Edge device runs on Windows or Linux, you need to access the installation packages through the proxy server. Depending on your operating system, follow the steps to install the IoT Edge runtime through a proxy server. 

### <a name="linux"></a>Linux

若要在 Linux 设备上安装 IoT Edge 运行时，请将包管理器配置为通过代理服务器访问安装包。 例如，[设置 apt-get 以使用 http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy)。 Once your package manager is configured, follow the instructions in [Install Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md) as usual.

### <a name="windows"></a>Windows

If you're installing the IoT Edge runtime on a Windows device, you need to go through the proxy server twice. The first connection downloads the installer script file, and the second connection is during the installation to download the necessary components. You can configure proxy information in Windows settings, or include your proxy information directly in the PowerShell commands. 

The following steps demonstrate an example of a windows installation using the `-proxy` argument:

1. The Invoke-WebRequest command needs proxy information to access the installer script. Then the Deploy-IoTEdge command needs the proxy information to download the installation files. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. The Initialize-IoTEdge command doesn't need to go through the proxy server, so the second step only requires proxy information for Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

如果拥有不能包含在 URL 中的复杂代理服务器凭据，请使用 `-InvokeWebRequestParameters` 中的 `-ProxyCredential` 参数。 例如，

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

有关代理参数的详细信息，请参阅 [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)。 For more information about Windows installation options, including offline installation, see [Install Azure IoT Edge runtime on Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>配置守护程序

IoT Edge relies on two daemons running on the IoT Edge device. The Moby daemon makes web requests to pull container images from container registries. IoT Edge 守护程序发出 Web 请求，以与 IoT 中心进行通信。

Both the Moby and the IoT Edge daemons need to be configured to use the proxy server for ongoing device functionality. This step takes place on the IoT Edge device during initial device setup. 

### <a name="moby-daemon"></a>Moby daemon

Since Moby is built on Docker, refer to the Docker documentation to configure the Moby daemon with environment variables. 大多数容器注册表（包括 DockerHub 和 Azure 容器注册表）支持 HTTPS 请求，因此，你应当设置的变量为 **HTTPS_PROXY**。 如果要从不支持传输层安全性 (TLS) 的注册表中拉取映像，则应当设置 **HTTP_PROXY**。 

Choose the article that applies to your IoT Edge device operating system: 

* [Configure Docker daemon on Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * The Moby daemon on Linux devices keeps the name Docker.
* [Configure Docker daemon on Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * The Moby daemon on Windows devices is called iotedge-moby. The names are different because it's possible to run both Docker Desktop and Moby in parallel on a Windows device. 

### <a name="iot-edge-daemon"></a>IoT Edge 守护程序

The IoT Edge daemon is configured in a similar manner to the Moby daemon. 使用以下步骤根据所使用的操作系统为服务设置环境变量。 

The IoT Edge daemon always uses HTTPS to send requests to IoT Hub.

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

This step takes place once on the IoT Edge device during initial device setup. 

1. 打开 IoT Edge 设备上的 config.yaml 文件。 在 Linux 系统上，此文件位于 /etc/iotedge/config.yaml。 在 Windows 系统上，此文件位于 C:\ProgramData\iotedge\config.yaml。 配置文件是受保护的，因此，你需要管理权限才能对其进行访问。 On Linux systems, use the `sudo` command before opening the file in your preferred text editor. On Windows, open a text editor like Notepad as administrator and then open the file. 

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

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>配置部署清单  

将 IoT Edge 设备配置为与代理服务器配合使用后，还需要在将来的部署清单中声明环境变量。 You can edit deployment manifests either using the Azure portal wizard or by editing a deployment manifest JSON file. 

始终配置两个运行时模块（edgeAgent 和 edgeHub），以通过代理服务器进行通信，从而维持与 IoT 中心的连接。 If you remove the proxy information from the edgeAgent module, the only way to reestablish connection is by editing the config.yaml file on the device, as described in the previous section. 

Other IoT Edge modules that connect to the internet should be configured to communicate through the proxy server, too. 但是，通过 edgeHub 路由其消息的模块或仅与设备上的其他模块通信的模块不需要代理服务器详细信息。 

This step is ongoing throughout the life of the IoT Edge device. 

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

