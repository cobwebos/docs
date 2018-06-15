---
title: Azure IoT Edge 疑难解答 | Microsoft Docs
description: 解决 Azure IoT Edge 的常见问题并学习疑难解答技能
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad22b0cd1457c1d4146a75047ff18e916c0c7ccd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633530"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge 的常见问题和解决方法

如果在你的环境中运行 Azure IoT Edge 时遇到问题，请使用本文作为指南来进行疑难解答并解决问题。 

## <a name="standard-diagnostic-steps"></a>标准诊断步骤 

遇到问题时，请通过查看容器日志和传递到以及来自设备的消息来详细了解 IoT Edge 设备的状态。 可以使用本部分中的命令和工具来收集信息。 

* 查看要检测问题的 docker 容器的日志。 先查看你的已部署容器，然后查看构成 IoT Edge 运行时的容器：Edge 代理和 Edge 中心。 Edge 代理日志通常提供有关每个容器的生命周期的信息。 Edge 中心日志提供有关消息传送和路由的信息。 

   ```cmd
   docker logs <container name>
   ```

* 查看经过 Edge 中心的消息，并通过来自运行时容器的详细 日志收集有关设备属性更新的见解。

   ```cmd
   iotedgectl setup --connection-string "{device connection string}" --runtime-log-level debug
   ```
   
* 使用 iotedgectl 命令查看详细日志：

   ```cmd
   iotedgectl --verbose DEBUG <command>
   ```

* 如果遇到连接问题，请检查边缘设备环境变量，例如设备连接字符串：

   ```cmd
   docker exec edgeAgent printenv
   ```

还可以检查在 IoT 中心与 IoT Edge 设备之间发送的消息。 可以使用适用于 Visual Studio Code 的 [Azure IoT 工具包](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)扩展查看这些消息。 有关更多指导，请参阅[Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)（通过 Azure IoT 进行开发时的趁手工具）。

在调查日志和消息来获得信息后，还可以尝试重新启动 Azure IoT Edge 运行时：

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>Edge 代理在大约一分钟后停止

Edge 代理启动并成功运行了大约一分钟，然后停止。 日志表明，Edge 代理尝试通过 AMQP 连接到 IoT 中心，并且在大约 30 秒后尝试使用 AMQP 通过 Websocket 进行连接。 当该操作失败时，Edge 代理退出。 

示例 Edge 代理日志：

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>根本原因
主机网络上的某个网络配置阻止 Edge 代理到达该网络。 代理首先会尝试通过 AMQP（端口 5671）进行连接。 如果此操作失败，它将尝试 Websocket（端口 443）。

IoT Edge 运行时会为每个模块设置要在其中进行通信的网络。 在 Linux 上，此网络是一个桥网络。 在 Windows 上，它使用 NAT。 此问题在其中的 Windows 容器使用 NAT 网络的 Windows 设备上更为常见。 

### <a name="resolution"></a>解决方法
确保分配给此桥/NAT 网络的 IP 地址具有通向 Internet 的路由。 有时候，主机上的 VPN 配置会替代 IoT Edge 网络。 

## <a name="edge-hub-fails-to-start"></a>Edge 中心未能启动

Edge 中心未能启动，并且向日志输出了以下消息： 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>根本原因
主机上的某个其他进程已绑定了端口 443。 Edge 中心映射端口 5671 和 443 以在网关方案中使用。 如果另一进程已绑定了此端口，则此端口映射会失败。 

### <a name="resolution"></a>解决方法
找到并停止正在使用端口 443 的进程。 此进程通常是 Web 服务器。

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Edge 代理无法访问某个模块的映像 (403)
某个容器未能运行，并且 Edge 代理日志显示了一个 403 错误。 

### <a name="root-cause"></a>根本原因
Edge 代理无权访问某个模块的映像。 

### <a name="resolution"></a>解决方法
尝试再次运行 `iotedgectl login` 命令。

## <a name="iotedgectl-cant-find-docker"></a>iotedgectl 找不到 Docker

`iotedgectl setup` 或 `iotedgectl start` 命令失败，并向日志输出了以下消息：
```output
File "/usr/local/lib/python2.7/dist-packages/edgectl/host/dockerclient.py", line 98, in get_os_type
  info = self._client.info()
File "/usr/local/lib/python2.7/dist-packages/docker/client.py", line 174, in info
  return self.api.info(*args, **kwargs)
File "/usr/local/lib/python2.7/dist-packages/docker/api/daemon.py", line 88, in info
  return self._result(self._get(self._url("/info")), True)
```

### <a name="root-cause"></a>根本原因
iotedgectl 找不到 Docker，而后者是先决条件。

### <a name="resolution"></a>解决方法
安装 Docker，确保其正在运行，然后重试。

## <a name="iotedgectl-setup-fails-with-an-invalid-hostname"></a>iotedgectl 设置失败，主机名无效

`iotedgectl setup` 命令失败，并输出了以下消息： 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>根本原因
IoT Edge 运行时只支持短于 64 个字符的主机名。 这对物理计算机来说不算什么问题，但是在虚拟机上设置运行时的时候可能会出现问题。 特别是为 Azure 中托管的 Windows 虚拟机自动生成的主机名，往往会很长。 

### <a name="resolution"></a>解决方法
看到此错误时，可以配置虚拟机的 DNS 名称，然后在设置命令中将 DNS 名称设置为主机名。

1. 在 Azure 门户中，导航到虚拟机的概述页面。 
2. 选择 DNS 名称下的“配置”。 如果你的虚拟机已配置 DNS 名称，则不需要再配置。 

   ![配置 DNS 名称](./media/troubleshoot/configure-dns.png)

3. 为“DNS 名称标签”提供一个值，然后选择“保存”。
4. 复制新的 DNS 名称，此名称应该为 \<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com。
5. 在虚拟机中使用下列命令，以 DNS 名称设置 IoT Edge 运行时：

   ```input
   iotedgectl setup --connection-string "<connection string>" --nopass --edge-hostname "<DNS name>"
   ```

## <a name="next-steps"></a>后续步骤
认为在 IoT Edge 平台中发现了 bug？ 请[提交问题](https://github.com/Azure/iot-edge/issues)以便我们可以持续改进。 
