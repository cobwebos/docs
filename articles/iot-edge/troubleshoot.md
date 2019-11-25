---
title: 故障排除 - Azure IoT Edge | Microsoft Docs
description: 使用本文了解 Azure IoT Edge 的标准诊断技能，例如检索组件状态和日志，以及解决常见问题
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cc05d820a409500e66e2525654954e4dddf48e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457261"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge 的常见问题和解决方法

如果在你的环境中运行 Azure IoT Edge 时遇到问题，请使用本文作为指南来进行疑难解答并解决问题。

## <a name="run-the-iotedge-check-command"></a>Run the iotedge 'check' command

Your first step when troubleshooting IoT Edge should be to use the `check` command, which performs a collection of configuration and connectivity tests for common issues. The `check` command is available in [release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) and later.

You can run the `check` command as follows, or include the `--help` flag to see a complete list of options:

* 在 Linux 上：

  ```bash
  sudo iotedge check
  ```

* 在 Windows 上：

  ```powershell
  iotedge check
  ```

The types of checks run by the tool can be classified as:

* Configuration checks: Examines details that could prevent Edge devices from connecting to the cloud, including issues with *config.yaml* and the container engine.
* Connection checks: Verifies the IoT Edge runtime can access ports on the host device and all the IoT Edge components can connect to the IoT Hub.
* Production readiness checks: Looks for recommended production best practices, such as the state of device certificate authority (CA) certificates and module log file configuration.

For a complete list of diagnostic checks, see [Built-in troubleshooting functionality](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="standard-diagnostic-steps"></a>标准诊断步骤

If you encounter an issue, you can learn more about the state of your IoT Edge device by reviewing the container logs and the messages that pass to and from the device. 可以使用本部分中的命令和工具来收集信息。

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Check the status of the IoT Edge Security Manager and its logs

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
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>如果 IoT Edge 安全管理器未运行，请验证 yaml 配置文件

> [!WARNING]
> YAML files cannot contain tabs as indentation. 请改用 2 个空格。

在 Linux 上：

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

在 Windows 上：

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>检查容器日志是否有问题

IoT Edge 安全守护程序运行后，请查看容器日志以检测问题。 Start with your deployed containers, then look at the containers that make up the IoT Edge runtime: edgeAgent and edgeHub. The IoT Edge agent logs typically provide info on the lifecycle of each container. The IoT Edge hub logs provide info on messaging and routing. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>View the messages going through the IoT Edge hub

You can view the messages going through the IoT Edge hub, and gather insights from verbose logs from the runtime containers. 若要在这些容器上启用详细日志，请在 yaml 配置文件中设置 `RuntimeLogLevel`。 若要打开该文件，请执行以下操作：

在 Linux 上：

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

在 Windows 上：

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

默认情况下，`agent` 元素将类似于以下示例：

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

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML 文件不能包含制表符作为缩进。 请改用 2 个空格。

保存该文件并重启 IoT Edge 安全管理器。

还可以检查在 IoT 中心与 IoT Edge 设备之间发送的消息。 使用用于 Visual Studio Code 的 [Azure IoT 中心工具包](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)扩展（以前称为 Azure IoT 工具包扩展）来查看这些消息。 有关详细信息，请参阅 [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)（通过 Azure IoT 进行开发时的顺手工具）。

### <a name="restart-containers"></a>重启容器
在为了解信息而调查日志和消息后，可以尝试重启容器：

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

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge agent stops after about a minute

The edgeAgent module starts and runs successfully for about a minute, then stops. The logs indicate that the IoT Edge agent attempts to connect to IoT Hub over AMQP, and then attempts to connect using AMQP over WebSocket. When that fails, the IoT Edge agent exits. 

Example edgeAgent logs:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

**Root cause**

A networking configuration on the host network is preventing the IoT Edge agent from reaching the network. 代理首先会尝试通过 AMQP（端口 5671）进行连接。 如果连接失败，它将尝试 WebSocket（端口 443）。

IoT Edge 运行时会为每个模块设置要在其中进行通信的网络。 在 Linux 上，此网络是一个桥网络。 在 Windows 上，它使用 NAT。 此问题在其中的 Windows 容器使用 NAT 网络的 Windows 设备上更为常见。 

**解决方法**

确保分配给此桥/NAT 网络的 IP 地址具有通向 Internet 的路由。 有时候，主机上的 VPN 配置会替代 IoT Edge 网络。 

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub fails to start

The edgeHub module fails to start, and prints the following message to the logs: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

**Root cause**

主机上的某个其他进程已绑定了端口 443。 The IoT Edge hub maps ports 5671 and 443 for use in gateway scenarios. 如果另一进程已绑定了此端口，则此端口映射会失败。 

**解决方法**

找到并停止正在使用端口 443 的进程。 此进程通常是 Web 服务器。

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge agent can't access a module's image (403)
A container fails to run, and the edgeAgent logs show a 403 error. 

**Root cause**

The Iot Edge agent doesn't have permissions to access a module's image. 

**解决方法**

确保在部署清单中正确指定了注册表凭据

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>由于主机名无效，IoT Edge 安全守护程序失败

`sudo journalctl -u iotedge` 命令失败，并输出了以下消息： 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Root cause**

IoT Edge 运行时只支持短于 64 个字符的主机名。 物理计算机通常不具有长主机名，但此问题在虚拟机上更常见。 特别是为 Azure 中托管的 Windows 虚拟机自动生成的主机名，往往会很长。 

**解决方法**

看到此错误时，可以配置虚拟机的 DNS 名称，然后在设置命令中将 DNS 名称设置为主机名。

1. 在 Azure 门户中，导航到虚拟机的概述页面。 
2. 选择 DNS 名称下的“配置”。 如果你的虚拟机已配置 DNS 名称，则不需要再配置。 

   ![配置虚拟机的 DNS 名称](./media/troubleshoot/configure-dns.png)

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

**Root cause**

The IoT Edge hub, which is part of the IoT Edge runtime, is optimized for performance by default and attempts to allocate large chunks of memory. 这种优化对于受限 Edge 设备并不理想，并可能会导致稳定性问题。

**解决方法**

For the IoT Edge hub, set an environment variable **OptimizeForPerformance** to **false**. 可通过两种方式实现此目的：

在 UI 中： 

In the portal, navigate to **Device Details** > **Set Modules** > **Configure advanced Edge Runtime settings**. Create an environment variable for the Edge Hub module called *OptimizeForPerformance* that is set to *false*.

![设为 false 的 OptimizeForPerformance](./media/troubleshoot/optimizeforperformance-false.png)

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
## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>无法在 Windows 上获取 IoT Edge 守护程序日志
如果在 Windows 上使用 `Get-WinEvent` 时收到 EventLogException，请检查注册表项。

**Root cause**

`Get-WinEvent` PowerShell 命令依赖于存在的注册表项来按特定 `ProviderName` 查找日志。

**解决方法**

设置 IoT Edge 守护程序的注册表项。 创建包含以下内容的 **iotedge.reg** 文件，再双击该文件或使用 `reg import iotedge.reg` 命令将其导入到 Windows 注册表中：

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge 模块无法将消息发送到 edgeHub 并出现 404 错误

自定义 IoT Edge 模块无法将消息发送到 edgeHub 并出现 404 `Module not found` 错误。 IoT Edge 守护程序在日志中输出以下消息： 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

**Root cause**

出于安全考虑，IoT Edge 守护程序会强制对连接到 edgeHub 的所有模块执行进程识别。 它会验证某个模块发送的所有消息是否来自该模块的主进程 ID。 如果发送消息的模块的进程 ID 不同于最初建立的进程 ID，则守护程序会拒绝该消息并返回 404 错误消息。

**解决方法**

As of version 1.0.7, all module processes are authorized to connect. If upgrading to 1.0.7 isn't possible, complete the following steps. For more information, see the [1.0.7 release changelog](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

确保自定义 IoT Edge 模块始终使用相同的进程 ID 向 Edge 中心发送消息。 For instance, make sure to `ENTRYPOINT` instead of `CMD` command in your Docker file, since `CMD` will lead to one process ID for the module and another process ID for the bash command running the main program whereas `ENTRYPOINT` will lead to a single process ID.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>IoT Edge 部署的防火墙和端口配置规则
Azure IoT Edge allows communication from an on-premises server to Azure cloud using supported IoT Hub protocols, see [choosing a communication protocol](../iot-hub/iot-hub-devguide-protocols.md). 为了增强安全性，Azure IoT Edge 与 Azure IoT 中心之间的信道始终配置为出站。 此配置基于[服务辅助通信模式](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)，可最大限度地减少恶意实体可探知的攻击面。 入站通信仅在特定情况下需要，其中 Azure IoT 中心需要将消息推送到 Azure IoT Edge 设备。 使用安全的 TLS 通道来保护云到设备的消息，并且可以使用 X.509 证书和 TPM 设备模块来增强其保护。 Azure IoT Edge 安全管理器控制这种通信的建立方式，具体请参阅 [IoT Edge 安全管理器](../iot-edge/iot-edge-security-manager.md)。

IoT Edge 提供增强的配置来保护 Azure IoT Edge 运行时和已部署的模块，但仍依赖于底层计算机和网络配置。 Hence, it is imperative to ensure proper network and firewall rules are set up for secure edge to cloud communication. The following table can be used as a guideline when configuration firewall rules for the underlying servers where Azure IoT Edge runtime is hosted:

|协议|Port|传入|传出|指南|
|--|--|--|--|--|
|MQTT|8883|阻止（默认）|阻止（默认）|<ul> <li>使用 MQTT 作为通信协议时，请将传出（出站）端口配置为“打开”。<li>IoT Edge 不支持将端口 1883 用于 MQTT。 <li>应阻止传入（入站）连接。</ul>|
|AMQP|5671|阻止（默认）|打开（默认）|<ul> <li>IoT Edge 的默认通信协议。 <li> 如果未为其他支持的协议配置 Azure IoT Edge，或者 AMQP 是所需的通信协议，则必须将此端口配置为“打开”。<li>IoT Edge 不支持将端口 5672 用于 AMQP。<li>当 Azure IoT Edge 使用不同的受 IoT 中心支持的协议时，请阻止此端口。<li>应阻止传入（入站）连接。</ul></ul>|
|HTTPS|443|阻止（默认）|打开（默认）|<ul> <li>将传出（出站）配置为在 443 上打开以进行 IoT Edge 预配。 使用手动脚本或 Azure IoT 设备预配服务 (DPS) 时，此配置是必需的。 <li>只应针对特定的方案打开传入（入站）连接： <ul> <li>  如果透明网关中的叶设备可能发送方法请求。 在这种情况下，无需向外部网络打开端口 443，即可连接到 IoT 中心或通过 Azure IoT Edge 提供 IoT 中心服务。 因此，传入规则可限制为只能从内部网络打开传入（入站）连接。 <li> 适用于客户端到设备 (C2D) 的方案。</ul><li>IoT Edge 不支持将端口 80 用于 HTTP。<li>如果无法在企业中配置非 HTTP 协议（例如 AMQP 或 MQTT），消息可通过 WebSocket 发送。 将这种情况下，将使用端口 443 进行 WebSocket 通信。</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge Agent module continually reports 'empty config file' and no modules start on the device

The device has trouble starting modules defined in the deployment. Only the edgeAgent is running but continually reporting 'empty config file...'.

**Root cause**

By default, IoT Edge starts modules in their own isolated container network. The device may be having trouble with DNS name resolution within this private network.

**解决方法**


**Option 1: Set DNS server in container engine settings**

Specify the DNS server for your environment in the container engine settings which will apply to all container modules started by the engine. Create a file named `daemon.json` specifying the DNS server to use. 例如：

```
{
    "dns": ["1.1.1.1"]
}
```

The above example sets the DNS server to a publicly accessible DNS service. If the edge device cannot access this IP from its environment, replace it with DNS server address that is accessible.

Place `daemon.json` in the right location for your platform: 

| 平台 | Location |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows host with Windows containers | `C:\ProgramData\iotedge-moby\config` |

If the location already contains `daemon.json` file, add the **dns** key to it and save the file.

*Restart the container engine for the updates to take effect*

| 平台 | 命令 |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Admin Powershell) | `Restart-Service iotedge-moby -Force` |

**Option 2: Set DNS server in IoT Edge deployment per module**

You can set DNS server for each module's *createOptions* in the IoT Edge deployment. 例如：

```
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Be sure to set this for the *edgeAgent* and *edgeHub* modules as well. 

## <a name="next-steps"></a>后续步骤
认为在 IoT Edge 平台中发现了 bug？ [提交问题](https://github.com/Azure/iotedge/issues)，以便我们可以持续改进。 

如果你还有其他问题，请创建[支持请求](https://portal.azure.com/#create/Microsoft.Support)以获取帮助。 

