---
title: Azure IoT Edge 疑难解答 | Microsoft Docs
description: 解决 Azure IoT Edge 的常见问题并学习疑难解答技能
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ecd19acdeba57a29a28187d42783bbf146095190
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001899"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge 的常见问题和解决方法

如果在你的环境中运行 Azure IoT Edge 时遇到问题，请使用本文作为指南来进行疑难解答并解决问题。 

## <a name="standard-diagnostic-steps"></a>标准诊断步骤 

遇到问题时，请通过查看容器日志和传递到以及来自设备的消息来详细了解 IoT Edge 设备的状态。 可以使用本部分中的命令和工具来收集信息。 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>检查 IoT Edge 安全管理器的状态及其日志：

在 Linux 上：
- 若要查看 IoT Edge 安全管理器的状态，请执行以下命令：

   ```bash
   sudo systemctl status iotedge
   ```

- 若要查看 IoT Edge 安全管理器的日志，请执行以下命令：

    ```bash
    sudo journalctl -u iotedge -f
    ```

- 若要查看 IoT Edge 安全管理器的更详细日志，请执行以下操作：

   - 编辑 iotedge 守护程序设置：

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - 更新以下行：
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - 重启 IoT Edge 安全守护程序：
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

在 Windows 上：
- 若要查看 IoT Edge 安全管理器的状态，请执行以下命令：

   ```powershell
   Get-Service iotedge
   ```

- 若要查看 IoT Edge 安全管理器的日志，请执行以下命令：

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false}
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>如果 IoT Edge 安全管理器未运行，请验证 yaml 配置文件

> [!WARNING]
> YAML 文件不能包含制表符作为缩进。 请改用 2 个空格。

在 Linux 上：

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

在 Windows 上：

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>检查容器日志是否有问题

IoT Edge 安全守护程序运行后，请查看容器日志以检测问题。 先查看你的已部署容器，然后查看构成 IoT Edge 运行时的容器：Edge 代理和 Edge 中心。 Edge 代理日志通常提供有关每个容器的生命周期的信息。 Edge 中心日志提供有关消息传送和路由的信息。 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-edge-hub"></a>查看经过 Edge 中心的消息

查看经过 Edge 中心的消息，并通过来自 edgeAgent 和 edgeHub 运行时容器的详细日志收集有关设备属性更新的见解。 若要在这些容器上启用详细日志，请在 yaml 配置文件中设置 `RuntimeLogLevel`。 若要打开该文件，请执行以下操作：

在 Linux 上：

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

在 Windows 上：

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

默认情况下，`agent` 元素将如下所示：

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

将 `env: {}` 替换为：

> [!WARNING]
> YAML 文件不能包含制表符作为缩进。 请改用 2 个空格。

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

保存该文件并重启 IoT Edge 安全管理器。

还可以检查在 IoT 中心与 IoT Edge 设备之间发送的消息。 可以使用适用于 Visual Studio Code 的 [Azure IoT 工具包](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)扩展查看这些消息。 有关更多指导，请参阅[Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)（通过 Azure IoT 进行开发时的趁手工具）。

### <a name="restart-containers"></a>重启容器
在调查日志和消息获得信息后，可以尝试重启容器：

```
iotedge restart <container name>
```

重启 IoT Edge 运行时容器：

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>重启 IoT Edge 安全管理器

如果问题仍然存在，可以尝试重启 IoT Edge 安全管理器。

在 Linux 上：

   ```cmd
   sudo systemctl restart iotedge
   ```

在 Windows 上：

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
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
确保在部署清单中正确指定了注册表凭据

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>由于主机名无效，IoT Edge 安全守护程序失败

`sudo journalctl -u iotedge` 命令失败，并输出了以下消息： 

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

   - 在 Linux 上：

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - 在 Windows 上：

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>有关资源受限设备的稳定性问题 
你可能会在 Raspberry Pi 等受限设备上遇到稳定性问题，尤其是在这些设备用作网关时。 症状包括 Edge 中心模块出现“内存不足”异常、下游设备无法连接或者设备在几小时后停止发送遥测消息。

### <a name="root-cause"></a>根本原因
Edge 中心是 Edge 运行时的一部分，默认情况下已针对性能进行了优化，并尝试分配大块内存。 这对于受限 Edge 设备并不理想，并可能会导致稳定性问题。

### <a name="resolution"></a>解决方法
对于 Edge 中心，请将环境变量 **OptimizeForPerformance** 设置为 **false**。 可通过两种方式实现此目的：

在 UI 中： 

在门户中，从“设备详细信息”->“设置模块”->“配置高级 Edge 运行时设置”，创建一个名为 *OptimizeForPerformance* 的环境变量，对于 *Edge 中心*将该变量设置为 *false*。

![optimizeforperformance][img-optimize-for-perf]

**或**

在部署清单中：

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```

## <a name="next-steps"></a>后续步骤
认为在 IoT Edge 平台中发现了 bug？ 请[提交问题](https://github.com/Azure/iotedge/issues)以便我们可以持续改进。 

<!-- Images -->
[img-optimize-for-perf]: ./media/troubleshoot/OptimizeForPerformanceFalse.png
