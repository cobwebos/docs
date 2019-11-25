---
title: 准备设备和部署以用于生产 - Azure IoT Edge | Microsoft Docs
description: Learn how to take your Azure IoT Edge solution from development to production, including setting up your devices with the appropriate certificates and making a deployment plan for future code updates.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1d8ba8452f5f2d4ab05083e1a97fa0b9ba75017f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457306"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>准备在生产环境中部署 IoT Edge 解决方案

如果已准备好将 IoT Edge 解决方案从开发环境转移到生产环境，请确保对其进行适当的配置，使其持续保持良好的性能。

The information provided in this article isn't all equal. 为帮助你优先处理某些任务，每个部分首先会提供一些列表，将准备工作划分为两个部分：转移到生产环境之前要实施的“重要说明”，以及需要知道的“有用提示”。

## <a name="device-configuration"></a>设备配置

IoT Edge 设备的类型多种多样，其中包括 Raspberry Pi、便携式计算机、服务器上运行的虚拟机，等等。 可通过物理方式或虚拟连接来访问设备，而设备也有可能长时间处于隔离状态。 Either way, you want to make sure that it's configured to work appropriately. 

* **重要说明**
    * 安装生产证书
    * 创建设备管理计划
    * 使用 Moby 作为容器引擎

* **有用提示**
    * 选择上游协议

### <a name="install-production-certificates"></a>安装生产证书

生产环境中的每个 IoT Edge 设备上需要安装设备证书颁发机构 (CA) 证书。 然后，在 config.yaml 文件中将该 CA 证书声明到 IoT Edge 运行时。 为使开发和测试变得更容易，如果 config.yaml 文件中未声明任何证书，IoT Edge 运行时会创建临时证书。 但是，这些临时证书将在三个月后过期，并且对于生产方案而言并不安全。 

若要了解设备 CA 证书的作用，请参阅 [Azure IoT Edge 如何使用证书](iot-edge-certs.md)。

有关如何在 IoT Edge 设备上安装证书并从 config.yaml 文件引用这些证书的详细信息，请参阅[将 IoT Edge 设备配置为充当透明网关](how-to-create-transparent-gateway.md)。 无论是否要将设备用作网关，配置证书的步骤都是相同的。 本文提供的脚本可以生成仅限测试的示例证书。 请不要在生产环境中使用这些示例证书。 

### <a name="have-a-device-management-plan"></a>创建设备管理计划

在将任何设备投放到生产环境之前，应该知道如何管理将来的更新。 对于 IoT Edge 设备，要更新的组件列表可能包括：

* 设备固件
* 操作系统库
* 容器引擎，例如 Moby
* IoT Edge 守护程序
* CA 证书

For more information, see [Update the IoT Edge runtime](how-to-update-iot-edge.md). 更新 IoT Edge 守护程序的当前方法需要通过物理方式或 SSH 访问 IoT Edge 设备。 If you have many devices to update, consider adding the update steps to a script or use an automation tool like Ansible.

### <a name="use-moby-as-the-container-engine"></a>使用 Moby 作为容器引擎

A container engine is a prerequisite for any IoT Edge device. 生产环境中仅支持 Moby 引擎。 其他容器引擎（例如 Docker）确实也能在 IoT Edge 上正常运行，但最好是将其用于开发。 与 Azure IoT Edge 配合使用时，可以重新分配 Moby 引擎，Microsoft 将为此引擎提供服务。

### <a name="choose-upstream-protocol"></a>选择上游协议

The protocol (and therefore the port used) for upstream communication to IoT Hub can be configured for both the IoT Edge agent and the IoT Edge hub. 默认协议为 AMQP，但可以根据网络设置更改协议。 

两个运行时模块都包含 **UpstreamProtocol** 环境变量。 该变量的有效值为： 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configure the UpstreamProtocol variable for the IoT Edge agent in the config.yaml file on the device itself. For example, if your IoT Edge device is behind a proxy server that blocks AMQP ports, you may need to configure the IoT Edge agent to use AMQP over WebSocket (AMQPWS) to establish the initial connection to IoT Hub. 

IoT Edge 设备建立连接后，请务必在将来的部署中继续为两个运行时模块配置 UpstreamProtocol 变量。 [将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)中提供了此过程的示例。

## <a name="deployment"></a>部署

* **有用提示**
    * 与上游协议保持一致
    * Set up host storage for system modules
    * Reduce memory space used by the IoT Edge hub
    * 不要使用模块映像的调试版本

### <a name="be-consistent-with-upstream-protocol"></a>与上游协议保持一致

If you configured the IoT Edge agent on your IoT Edge device to use a different protocol than the default AMQP, then you should declare the same protocol in all future deployments. 例如，如果 IoT Edge 设备位于阻止 AMQP 端口的代理服务器后面，则你可能已将设备配置为通过基于 WebSocket 的 AMQP (AMQPWS) 进行连接。 When you deploy modules to the device, configure the same AMQPWS protocol for the IoT Edge agent and IoT Edge hub, or else the default AMQP will override the settings and prevent you from connecting again. 

You only have to configure the UpstreamProtocol environment variable for the IoT Edge agent and IoT Edge hub modules. 其他任何模块将采用运行时模块中设置的任何协议。 

[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)中提供了此过程的示例。

### <a name="set-up-host-storage-for-system-modules"></a>Set up host storage for system modules

The IoT Edge hub and agent modules use local storage to maintain state and enable messaging between modules, devices, and the cloud. For better reliability and performance, configure the system modules to use storage on the host filesystem.

For more information, see [Host storage for system modules](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Reduce memory space used by IoT Edge hub

If you're deploying constrained devices with limited memory available, you can configure IoT Edge hub to run in a more streamlined capacity and use less disk space. These configurations do limit the performance of the IoT Edge hub, however, so find the right balance that works for your solution. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>在受限的设备上不要进行性能优化

The IoT Edge hub is optimized for performance by default, so it attempts to allocate large chunks of memory. 在 Raspberry Pi 等小型设备上，此配置可能会影响稳定性。 If you're deploying devices with constrained resources, you may want to set the **OptimizeForPerformance** environment variable to **false** on the IoT Edge hub. 

When **OptimizeForPerformance** is set to **true**, the MQTT protocol head uses the PooledByteBufferAllocator which has better performance but allocates more memory. The allocator does not work well on 32 bit operating systems or on devices with low memory. Additionally, when optimized for performance, RocksDb allocates more memory for its role as the local storage provider. 

有关详细信息，请参阅[资源受限设备的稳定性问题](troubleshoot.md#stability-issues-on-resource-constrained-devices)。

#### <a name="disable-unused-protocols"></a>禁用未使用的协议

Another way to optimize the performance of the IoT Edge hub and reduce its memory usage is to turn off the protocol heads for any protocols that you're not using in your solution. 

Protocol heads are configured by setting boolean environment variables for the IoT Edge hub module in your deployment manifests. 三个变量如下：

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

所有三个变量都带有两条下划线，可设置为 true 或 false。 

#### <a name="reduce-storage-time-for-messages"></a>减少消息的存储时间

The IoT Edge hub module stores messages temporarily if they cannot be delivered to IoT Hub for any reason. You can configure how long the IoT Edge hub holds on to undelivered messages before letting them expire. If you have memory concerns on your device, you can lower the **timeToLiveSecs** value in the IoT Edge hub module twin. 

timeToLiveSecs 参数的默认值为 7200 秒，即 2 小时。 

### <a name="do-not-use-debug-versions-of-module-images"></a>不要使用模块映像的调试版本

从测试方案转移到生产方案时，请记得从部署清单中删除调试配置。 确保部署清单中没有任何模块映像带有 **\.debug** 后缀。 如果添加了 create 选项用于公开模块中的调试端口，也请删除这些 create 选项。 

## <a name="container-management"></a>容器管理

* **重要说明**
    * 管理对容器注册表的访问
    * 使用标记管理版本

### <a name="manage-access-to-your-container-registry"></a>管理对容器注册表的访问

在将模块部署到生产 IoT Edge 设备之前，请务必控制对容器注册表的访问，使外部用户无法访问容器映像或对其进行更改。 使用专用（而不是公共）容器注册表来管理容器映像。 

教程和其他文档会指导你在 IoT Edge 设备上使用开发计算机上所用的相同容器注册表凭据。 这些说明旨在帮助你更轻松地设置测试和开发环境，在生产方案中请勿遵照这些说明。 与 IoT Edge 设备一样，当应用程序或服务以自动方式或无人参与的方式提取容器映像时，Azure 容器注册表建议[使用服务主体进行身份验证](../container-registry/container-registry-auth-service-principal.md)。 创建一个对容器注册表拥有只读访问权限的服务主体，并在部署清单中提供该用户名和密码。

### <a name="use-tags-to-manage-versions"></a>使用标记管理版本

A tag is a docker concept that you can use to distinguish between versions of docker containers. 标记是附加在容器存储库末尾的后缀（如 **1.0**）。 例如 **mcr.microsoft.com/azureiotedge-agent:1.0**。 标记是可变的，随时可能更改为指向另一容器，因此，团队应该议定一种约定，以便今后在更新模块映像时遵循。 

标记还可帮助你针对 IoT Edge 设备强制实施更新。 将模块的更新版本推送到容器注册表时，请递增标记。 然后，使用递增的标记将新部署推送到设备。 容器引擎将递增的标记识别为新版本，并将最新模块版本提取到设备。 

有关标记约定的示例，请参阅[更新 IoT Edge 运行时](how-to-update-iot-edge.md#understand-iot-edge-tags)，了解 IoT Edge 如何使用滚动更新标记和特定标记来跟踪版本。 

## <a name="networking"></a>网络

* **有用提示**
    * 检查出站/入站配置
    * Allow connections from IoT Edge devices
    * 配置为通过代理进行通信

### <a name="review-outboundinbound-configuration"></a>检查出站/入站配置

Azure IoT 中心与 IoT Edge 之间的信道始终配置为出站。 对于大多数 IoT Edge 方案，只需建立三个连接。 容器引擎需要连接到保存模块映像的一个或多个容器注册表。 IoT Edge 运行时需要连接到 IoT 中心，以检索设备配置信息，以及发送消息和遥测数据。 如果使用自动预配，则 IoT Edge 守护程序需要连接到设备预配服务。 有关详细信息，请参阅[防火墙和端口配置规则](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment)。

### <a name="allow-connections-from-iot-edge-devices"></a>Allow connections from IoT Edge devices

If your networking setup requires that you explicitly permit connections made from IoT Edge devices, review the following list of IoT Edge components:

* **IoT Edge 代理**可能通过 WebSocket 来与 IoT 中心建立持久性 AMQP/MQTT 连接。 
* **IoT Edge 中心**可能通过 WebSocket 来与 IoT 中心建立一个持久性 AMQP 连接或多个 MQTT 连接。 
* **IoT Edge 守护程序**向 IoT 中心发出间歇性 HTTPS 调用。 

在所有三种情况下，DNS 名称将与 \*azure-devices.net 模式匹配。 

此外，**容器引擎**通过 HTTPS 向容器注册表发出调用。 若要检索 IoT Edge 运行时容器映像，请使用 DNS 名称 mcr.microsoft.com。 容器引擎连接到部署中配置的其他注册表。 

此清单可作为防火墙规则的入手点：

   | URL（\* = 通配符） | 出站 TCP 端口 | 使用情况 |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft 容器注册表 |
   | global.azure-devices-provisioning.net  | 443 | DPS 访问（可选） |
   | \*.azurecr.io | 443 | Personal and third-party container registries |
   | \*.blob.core.windows.net | 443 | Download Azure Container Registry image deltas from blob storage  | 
   | \*.azure-devices.net | 5671、8883、443 | IoT 中心访问 |
   | \*.docker.io  | 443 | Docker Hub access (optional) |

Some of these firewall rules are inherited from Azure Container Registry. For more information, see [Configure rules to access an Azure container registry behind a firewall](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>配置为通过代理进行通信

如果要在使用代理服务器的网络中部署设备，这些设备需要能够通过该代理进行通信，这样才能访问 IoT 中心和容器注册表。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。

## <a name="solution-management"></a>解决方案管理

* **有用提示**
    * 设置日志和诊断
    * 考虑测试和 CI/CD 管道

### <a name="set-up-logs-and-diagnostics"></a>设置日志和诊断

On Linux, the IoT Edge daemon uses journals as the default logging driver. 可以使用命令行工具 `journalctl` 查询守护程序日志。 在 Windows 上，IoT Edge 守护程序使用 PowerShell 诊断。 使用 `Get-IoTEdgeLog` 可以查询守护程序的日志。 IoT Edge modules use the JSON driver for logging, which is the  default.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

测试 IoT Edge 部署时，通常可以访问设备来检索日志和进行故障排除。 在部署方案中，可能做不到这一点。 考虑如何收集有关生产环境中设备的信息。 一种做法是使用日志记录模块从其他模块收集信息，然后将其发送到云中。 日志记录模块的一个示例是 [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics)，你也可以设计自己的模块。 

### <a name="place-limits-on-log-size"></a>Place limits on log size

By default the Moby container engine does not set container log size limits. Over time this can lead to the device filling up with logs and running out of disk space. Consider the following options to prevent this:

**Option: Set global limits that apply to all container modules**

You can limit the size of all container logfiles in the container engine log options. The following example sets the log driver to `json-file` (recommended) with limits on size and number of files:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Add (or append) this information to a file named `daemon.json` and place it the right location for your device platform.

| 平台 | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

The container engine must be restarted for the changes to take effect.

**Option: Adjust log settings for each container module**

You can do so in the **createOptions** of each module. 例如：

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

**Additional options on Linux systems**

* Configure the container engine to send logs to `systemd` [journal](https://docs.docker.com/config/containers/logging/journald/) by setting `journald` as the default logging driver. 

* Periodically remove old logs from your device by installing a logrotate tool. 使用以下文件规范： 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>考虑测试和 CI/CD 管道

为使 IoT Edge 部署方案获得最大的效率，请考虑将生产部署集成到测试和 CI/CD 管道中。 Azure IoT Edge 支持多个 CI/CD 平台，包括 Azure DevOps。 有关详细信息，请参阅[向 Azure IoT Edge 进行持续集成和持续部署](how-to-ci-cd.md)。

## <a name="next-steps"></a>后续步骤

* 了解有关 [IoT Edge 自动部署](module-deployment-monitoring.md)的详细信息。
* 了解 IoT Edge 如何支持[持续集成和持续部署](how-to-ci-cd.md)。
