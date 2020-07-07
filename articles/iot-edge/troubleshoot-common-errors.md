---
title: 常见错误 - Azure IoT Edge | Microsoft Docs
description: 本文介绍了部署 IoT Edge 解决方案时遇到的问题的常见解决方案
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ed93d24bc06a6622a8ace2b0ab6b44582da001c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82783742"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge 的常见问题和解决方法

还列出了在部署 IoT Edge 解决方案时可能遇到的常见问题的步骤。 如需了解如何从 IoT Edge 设备查找日志和错误，请参阅[对 IoT Edge 设备进行故障排除](troubleshoot.md)。

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge 代理在大约一分钟后停止

**观察到的行为：**

edgeAgent 模块将启动并成功运行大约一分钟，然后停止。 日志表明，IoT Edge 代理尝试通过 AMQP 连接到 IoT 中心，并且尝试使用 AMQP 通过 WebSocket 进行连接。 该操作失败时，IoT Edge 代理将会退出。

示例 edgeAgent 日志：

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**根本原因：**

主机网络上的某个网络配置阻止 IoT Edge 代理到达该网络。 代理首先会尝试通过 AMQP（端口 5671）进行连接。 如果连接失败，它将尝试 WebSocket（端口 443）。

IoT Edge 运行时会为每个模块设置要在其中进行通信的网络。 在 Linux 上，此网络是一个桥网络。 在 Windows 上，它使用 NAT。 此问题在其中的 Windows 容器使用 NAT 网络的 Windows 设备上更为常见。

**解决方法：**

确保分配给此桥/NAT 网络的 IP 地址具有通向 Internet 的路由。 有时候，主机上的 VPN 配置会替代 IoT Edge 网络。

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge 代理无法访问某个模块的映像 (403)

**观察到的行为：**

某个容器未能运行，并且 edgeAgent 日志显示了 403 错误。

**根本原因：**

IoT Edge 代理无权访问某个模块的映像。

**解决方法：**

确保在部署清单中正确指定了注册表凭据。

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge 代理模块报告“配置文件为空”，且设备上不会启动任何模块

**观察到的行为：**

设备在启动部署中定义的模块时出现问题。 只有 edgeAgent 在运行，但它持续报告“配置文件为空...”。

**根本原因：**

默认情况下，IoT Edge 在模块自身的隔离容器网络中启动模块。 在此专用网络中，设备可能会遇到 DNS 名称解析方面的问题。

**解决方法：**

**选项 1：在容器引擎设置中设置 DNS 服务器**

在容器引擎设置中为环境指定 DNS 服务器，该设置将应用于引擎启动的所有容器模块。 创建名为 `daemon.json` 的文件，并在其中指定要使用的 DNS 服务器。 例如：

```json
{
    "dns": ["1.1.1.1"]
}
```

上面的示例将 DNS 服务器设置为可公开访问的 DNS 服务。 如果边缘设备无法从其所在环境访问此 IP，请将其替换为可访问的 DNS 服务器地址。

将 `daemon.json` 放入平台上的适当位置：

| 平台 | 位置 |
| --------- | -------- |
| Linux | `/etc/docker` |
| 包含 Windows 容器的 Windows 主机 | `C:\ProgramData\iotedge-moby\config` |

如果该位置已包含 `daemon.json` 文件，请在其中添加 **dns** 密钥，然后保存该文件。

重启容器引擎以使更新生效。

| 平台 | 命令 |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Admin PowerShell) | `Restart-Service iotedge-moby -Force` |

**选项 2：在每个模块的 IoT Edge 部署中设置 DNS 服务器**

可以针对 IoT Edge 部署中每个模块的 *createOptions* 设置 DNS 服务器。 例如：

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

请确保也为 edgeAgent 和 edgeHub 模块设置此配置。

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge 中心未能启动

**观察到的行为：**

edgeHub 模块未能启动。 你可能会在日志中看到一条类似于以下错误的消息：

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

或

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**根本原因：**

主机上的其他某个进程绑定了 edgeHub 模块尝试绑定的端口。 用于网关方案的 IoT Edge 中心映射端口 443、5671 和 8883。 如果另一个进程已绑定了其中某个端口，则无法启动该模块。

**解决方法：**

可通过两种方式解决此问题：

如果 IoT Edge 设备充当网关设备，则你需要查找并停止正在使用端口 443、5671 或 8883 的进程。 端口 443 的错误通常表示另一个进程是 Web 服务器。

如果不需要将 IoT Edge 设备用作网关，则可以从 edgeHub 的模块创建选项中删除端口绑定。 可以在 Azure 门户中更改创建选项，也可直接在 deployment 文件中进行更改。

在 Azure 门户中：

1. 导航到 IoT 中心并选择“IoT Edge”。

2. 选择要更新的 IoT Edge 设备。

3. 选择“设置模块”。

4. 选择“运行时设置”。

5. 在“Edge Hub”模块设置中，从“创建选项”文本框中删除所有内容。

6. 保存更改并创建部署。

在 deployment.json 文件中：

1. 打开应用到 IoT Edge 设备的 deployment.json 文件。

2. 在 edgeAgent 所需属性部分找到 `edgeHub` 设置：

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. 删除 `createOptions` 行，并删除其前面的 `image` 行的尾随逗号：

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. 保存该文件，然后再次将其应用到 IoT Edge 设备。

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>由于主机名无效，IoT Edge 安全守护程序失败

**观察到的行为：**

尝试[检查 IoT Edge 安全管理器日志](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)失败，并输出以下消息：

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**根本原因：**

IoT Edge 运行时只支持短于 64 个字符的主机名。 物理计算机通常不具有长主机名，但此问题在虚拟机上更常见。 特别是为 Azure 中托管的 Windows 虚拟机自动生成的主机名，往往会很长。

**解决方法：**

看到此错误时，可以配置虚拟机的 DNS 名称，然后在设置命令中将 DNS 名称设置为主机名。

1. 在 Azure 门户中，导航到虚拟机的概述页面。
2. 选择 DNS 名称下的“配置”。 如果你的虚拟机已配置 DNS 名称，则不需要再配置。

   ![配置虚拟机的 DNS 名称](./media/troubleshoot/configure-dns.png)

3. 为“DNS 名称标签”提供一个值，然后选择“保存” 。
4. 复制新的 DNS 名称，名称格式应为 \<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com。
5. 在虚拟机中使用下列命令，以 DNS 名称设置 IoT Edge 运行时：

   * 在 Linux 上：

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * 在 Windows 上：

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>无法在 Windows 上获取 IoT Edge 守护程序日志

**观察到的行为：**

在 Windows 上使用 `Get-WinEvent` 时，会收到 EventLogException。

**根本原因：**

`Get-WinEvent` PowerShell 命令依赖于存在的注册表项来按特定 `ProviderName` 查找日志。

**解决方法：**

设置 IoT Edge 守护程序的注册表项。 创建包含以下内容的 **iotedge.reg** 文件，再双击该文件或使用 `reg import iotedge.reg` 命令将其导入到 Windows 注册表中：

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="stability-issues-on-smaller-devices"></a>小型设备上的稳定性问题

**观察到的行为：**

你可能会在 Raspberry Pi 等资源受限设备上遇到稳定性问题，尤其是在这些设备用作网关时。 症状包括 IoT Edge 中心模块出现“内存不足”异常、下游设备无法连接或者设备在几小时后无法发送遥测消息。

**根本原因：**

IoT Edge 中心是 IoT Edge 运行时的一部分，默认情况下已针对性能进行了优化，并尝试分配大块内存。 这种优化对于受限 Edge 设备并不理想，并可能会导致稳定性问题。

**解决方法：**

对于 IoT Edge 中心，请将环境变量 **OptimizeForPerformance** 设置为 **false**。 可以通过两种方式来设置环境变量：

在 Azure 门户中：

在 IoT 中心，选择 IoT Edge 设备，然后从设备详细信息页中依次选择“设置模块” > “运行时设置” 。 为 IoT Edge 中心模块创建名为“OptimizeForPerformance”、设置为“false”的环境变量。

![设为 false 的 OptimizeForPerformance](./media/troubleshoot/optimizeforperformance-false.png)

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

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>IoT Edge 模块未能将消息发送到 edgeHub 并出现 404 错误

**观察到的行为：**

自定义 IoT Edge 模块未能将消息发送到 IoT Edge 中心并出现 404 `Module not found` 错误。 IoT Edge 守护程序在日志中输出以下消息：

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**根本原因：**

出于安全考虑，IoT Edge 守护程序会强制对连接到 edgeHub 的所有模块执行进程识别。 它会验证某个模块发送的所有消息是否来自该模块的主进程 ID。 如果发送消息的模块的进程 ID 不同于最初建立的进程 ID，则守护程序会拒绝该消息并返回 404 错误消息。

**解决方法：**

从版本 1.0.7 开始，所有模块进程都有权进行连接。 有关详细信息，请参阅 [1.0.7 版本更改日志](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1)。

如果无法升级到 1.0.7，请完成以下步骤。 确保自定义 IoT Edge 模块始终使用相同的进程 ID 向 Edge 中心发送消息。 例如，请确保在 Docker 文件中使用 `ENTRYPOINT`，而不使用 `CMD` 命令。 `CMD` 命令会导致为模块生成一个进程 ID，并为运行主程序的 bash 命令生成另一个进程 ID，但是 `ENTRYPOINT` 只会生成单个进程 ID。

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>IoT Edge 模块部署成功后，会从设备中消失

**观察到的行为：**

为 IoT Edge 设备设置模块后即表示已成功部署模块，但几分钟后，它们将从设备以及 Azure 门户中的设备详细信息中消失。 除了定义的模块外，其他模块也可能会出现在设备上。

**根本原因：**

如果针对某个设备进行自动部署，则自动部署的优先级高于为单个设备手动设置模块。 Azure 门户中的“设置模块”功能或 Visual Studio Code 中的“为单个设备创建部署”功能将暂时生效 。 你会看到定义的模块在设备上启动。 然后，自动部署的优先级开始生效，并覆盖该设备的所需属性。

**解决方法：**

每个设备仅使用一种类型的部署机制，即自动部署或单设备部署。 如果你有针对某个设备的多个自动部署，则可以更改优先级或目标说明，以确保正确的部署应用于给定的设备。 还可以更新设备孪生，使其不再与自动部署的目标描述匹配。

有关详细信息，请参阅[了解单个设备或大规模的 IoT Edge 自动部署](module-deployment-monitoring.md)。

## <a name="next-steps"></a>后续步骤

认为在 IoT Edge 平台中发现了 bug？ [提交问题](https://github.com/Azure/iotedge/issues)，以便我们可以持续改进。

如果你还有其他问题，请创建[支持请求](https://portal.azure.com/#create/Microsoft.Support)以获取帮助。
