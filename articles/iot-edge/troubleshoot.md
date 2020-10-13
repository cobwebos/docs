---
title: 故障排除 - Azure IoT Edge | Microsoft Docs
description: 通过本文了解 Azure IoT Edge 的标准诊断技能，例如检索组件状态和日志
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0e4ec7127df288ec1818df307da1ea9824141309
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87902450"
---
# <a name="troubleshoot-your-iot-edge-device"></a>排除 IoT Edge 设备故障

如果在环境中运行 Azure IoT Edge 时遇到问题，请将本文作为指南进行故障排除和诊断。

## <a name="run-the-check-command"></a>运行“check”命令

排查 IoT Edge 问题时，第一步应该是使用 `check` 命令，针对常见问题运行一系列配置和连接性测试。 `check` 命令在[版本 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) 及更高版本中提供。

>[!NOTE]
>如果 IoT Edge 设备位于代理服务器后面，则故障排除工具无法运行连接性检查。

可以运行 `check` 命令（如下所示），也可以包括 `--help` 标志，以便查看选项的完整列表：

在 Linux 上：

```bash
sudo iotedge check
```

在 Windows 上：

```powershell
iotedge check
```

故障排除工具将运行多个检查，这些检查分为以下三个类别：

* “配置检查”将检查妨碍 IoT Edge 设备连接到云的详细情况，包括 config.yaml 和容器引擎出现的问题。 
* “连接性检查”将验证 IoT Edge 运行时能否访问主机设备上的端口，以及所有 IoT Edge 组件能否连接到 IoT 中心。 如果 IoT Edge 设备位于代理后面，则这组检查将返回错误。
* “生产准备情况检查”将寻找建议的生产最佳做法，例如设备证书颁发机构 (CA) 颁发证书的状态以及模块日志文件配置。

有关此工具运行的每个诊断检查的信息，包括可在出现错误或警告时执行的操作，请参阅 [IoT Edge 排除故障检查](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)。

## <a name="gather-debug-information-with-support-bundle-command"></a>通过“support-bundle”命令收集调试信息

需要从 IoT Edge 设备收集日志时，最方便的方法是使用 `support-bundle` 命令。 默认情况下，此命令收集模块、IoT Edge 安全管理器和容器引擎日志、`iotedge check` JSON 输出和其他有用的调试信息。 它将它们压缩成单个文件，便于共享。 `support-bundle` 命令在[版本 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) 及更高版本中提供。

运行带 `--since` 标志的 `support-bundle` 命令，指定要从过去获取日志的时间。 例如，`6h` 会获取过去 6 小时的日志，`6d` 会获取过去 6 天的日志，`6m` 会获取过去 6 分钟的日志，依此类推。 包括 `--help` 标志即可查看选项的完整列表。

在 Linux 上：

```bash
sudo iotedge support-bundle --since 6h
```

在 Windows 上：

```powershell
iotedge support-bundle --since 6h
```

> [!WARNING]
> `support-bundle` 命令的输出可能包含主机、设备和模块名称、模块记录的信息，等等。如果在公共论坛中共享输出，请注意这一点。

## <a name="check-your-iot-edge-version"></a>检查 IoT Edge 版本

如果运行的是较旧版本的 IoT Edge，则升级可能会解决你的问题。 `iotedge check` 工具将检查 IoT Edge 安全守护程序是否是最新版本，但不会检查 IoT Edge 集中心和代理模块的版本。 若要检查设备上的运行时模块的版本，请使用 `iotedge logs edgeAgent` 和 `iotedge logs edgeHub` 命令。 模块启动时，版本号会在日志中显示。

有关如何升级设备的说明，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>检查 IoT Edge 安全管理器的状态及其日志

[IoT Edge 安全管理器](iot-edge-security-manager.md) 负责在启动时初始化 IoT Edge 系统和预配设备等操作。 如果 IoT Edge 未启动，则安全管理器日志可能会提供有用的信息。

在 Linux 上：

* 查看 IoT Edge 安全管理器的状态：

   ```bash
   sudo systemctl status iotedge
   ```

* 查看 IoT Edge 安全管理器的日志：

    ```bash
    sudo journalctl -u iotedge -f
    ```

* 查看 IoT Edge 安全管理器的更详细日志：

  * 编辑 IoT Edge 守护程序设置：

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * 更新以下行：

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * 重启 IoT Edge 安全守护程序：

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

在 Windows 上：

* 查看 IoT Edge 安全管理器的状态：

   ```powershell
   Get-Service iotedge
   ```

* 查看 IoT Edge 安全管理器的日志：

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* 仅查看最后 5 分钟的 IoT Edge 安全管理器日志：

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* 查看 IoT Edge 安全管理器的更详细日志：

  * 添加系统级环境变量：

      ```powershell
      [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
      ```

  * 重启 IoT Edge 安全守护程序：

      ```powershell
      Restart-Service iotedge
      ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>如果 IoT Edge 安全管理器未运行，请验证 yaml 配置文件

> [!WARNING]
> YAML 文件不能包含制表符作为缩进。 请改用 2 个空格。 顶级元素应该没有前导空格。

在 Linux 上：

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

在 Windows 上：

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
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

## <a name="check-container-logs-for-issues"></a>检查容器日志是否有问题

IoT Edge 安全守护程序运行后，请查看容器日志以检测问题。 先查看你的已部署容器，然后查看构成 IoT Edge 运行时的容器：edgeAgent 和 edgeHub。 IoT Edge 代理日志通常提供有关每个容器的生命周期的信息。 IoT Edge 中心日志提供有关消息传送和路由的信息。

```cmd
iotedge logs <container name>
```

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>查看通过 IoT Edge 中心的消息

查看通过 IoT Edge 中心的消息，并通过来自运行时容器的详细日志收集见解。 若要在这些容器上启用详细日志，请在 yaml 配置文件中设置 `RuntimeLogLevel`。 若要打开该文件，请执行以下操作：

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
   > YAML 文件不能包含制表符作为缩进。 请改用 2 个空格。 顶级项不能有前导空格。

保存该文件并重启 IoT Edge 安全管理器。

还可以检查在 IoT 中心与 IoT Edge 设备之间发送的消息。 使用[适用于 Visual Studio Code 的 Azure IoT 中心扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)查看这些消息。 有关详细信息，请参阅 [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)（通过 Azure IoT 进行开发时的顺手工具）。

## <a name="restart-containers"></a>重启容器

在为了解信息而调查日志和消息后，可以尝试重启容器：

```cmd
iotedge restart <container name>
```

重启 IoT Edge 运行时容器：

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>检查防火墙和端口配置规则

Azure IoT Edge 允许使用支持的 IoT 中心协议从本地服务器来与 Azure 云通信，具体请参阅[选择通信协议](../iot-hub/iot-hub-devguide-protocols.md)。 为了增强安全性，Azure IoT Edge 与 Azure IoT 中心之间的信道始终配置为出站。 此配置基于[服务辅助通信模式](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)，可最大限度地减少恶意实体可探知的攻击面。 入站通信仅在特定情况下需要，其中 Azure IoT 中心需要将消息推送到 Azure IoT Edge 设备。 使用安全的 TLS 通道来保护云到设备的消息，并且可以使用 X.509 证书和 TPM 设备模块来增强其保护。 Azure IoT Edge 安全管理器控制这种通信的建立方式，具体请参阅 [IoT Edge 安全管理器](../iot-edge/iot-edge-security-manager.md)。

IoT Edge 提供增强的配置来保护 Azure IoT Edge 运行时和已部署的模块，但仍依赖于底层计算机和网络配置。 因此，必须确保设置适当的网络和防火墙规则来保护 Edge 与云之间的通信。 为托管 Azure IoT Edge 运行时的底层服务器配置防火墙规则时，可参考下表中的指导：

|协议|端口|传入|传出|指南|
|--|--|--|--|--|
|MQTT|8883|阻止（默认）|阻止（默认）|<ul> <li>使用 MQTT 作为通信协议时，请将传出（出站）端口配置为“打开”。<li>IoT Edge 不支持将端口 1883 用于 MQTT。 <li>应阻止传入（入站）连接。</ul>|
|AMQP|5671|阻止（默认）|打开（默认）|<ul> <li>IoT Edge 的默认通信协议。 <li> 如果未为其他支持的协议配置 Azure IoT Edge，或者 AMQP 是所需的通信协议，则必须将此端口配置为“打开”。<li>IoT Edge 不支持将端口 5672 用于 AMQP。<li>当 Azure IoT Edge 使用不同的受 IoT 中心支持的协议时，请阻止此端口。<li>应阻止传入（入站）连接。</ul></ul>|
|HTTPS|443|阻止（默认）|打开（默认）|<ul> <li>将传出（出站）配置为在 443 上打开以进行 IoT Edge 预配。 使用手动脚本或 Azure IoT 设备预配服务 (DPS) 时，此配置是必需的。 <li>只应针对特定的方案打开传入（入站）连接： <ul> <li>  如果透明网关中的叶设备可能发送方法请求。 在这种情况下，无需向外部网络打开端口 443，即可连接到 IoT 中心或通过 Azure IoT Edge 提供 IoT 中心服务。 因此，传入规则可限制为只能从内部网络打开传入（入站）连接。 <li> 适用于客户端到设备 (C2D) 的方案。</ul><li>IoT Edge 不支持将端口 80 用于 HTTP。<li>如果无法在企业中配置非 HTTP 协议（例如 AMQP 或 MQTT），消息可通过 WebSocket 发送。 将这种情况下，将使用端口 443 进行 WebSocket 通信。</ul>|

## <a name="next-steps"></a>后续步骤

认为在 IoT Edge 平台中发现了 bug？ [提交问题](https://github.com/Azure/iotedge/issues)，以便我们可以持续改进。

如果你还有其他问题，请创建[支持请求](https://portal.azure.com/#create/Microsoft.Support)以获取帮助。
