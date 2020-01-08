---
title: 准备在生产环境中部署解决方案-Azure IoT Edge
description: 了解如何从开发到生产中获取 Azure IoT Edge 解决方案，包括使用适当的证书设置设备，并为未来的代码更新制定部署计划。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1213ab4c623cb1d475dff1d71e65439b1d08f5c1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429436"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>准备在生产环境中部署 IoT Edge 解决方案

如果已准备好将 IoT Edge 解决方案从开发环境转移到生产环境，请确保对其进行适当的配置，使其持续保持良好的性能。

本文中提供的信息并不完全相同。 为帮助你优先处理某些任务，每个部分首先会提供一些列表，将准备工作划分为两个部分：转移到生产环境之前要实施的“重要说明”，以及需要知道的“有用提示”。

## <a name="device-configuration"></a>设备配置

IoT Edge 设备的类型多种多样，其中包括 Raspberry Pi、便携式计算机、服务器上运行的虚拟机，等等。 可通过物理方式或虚拟连接来访问设备，而设备也有可能长时间处于隔离状态。 无论采用哪种方式，都要确保将其配置为相应工作。 

* **重要说明**
    * 安装生产证书
    * 创建设备管理计划
    * 使用 Moby 作为容器引擎

* **有用提示**
    * 选择上游协议

### <a name="install-production-certificates"></a>安装生产证书

生产环境中的每个 IoT Edge 设备上需要安装设备证书颁发机构 (CA) 证书。 然后，在 config.yaml 文件中将该 CA 证书声明到 IoT Edge 运行时。 为使开发和测试变得更容易，如果 config.yaml 文件中未声明任何证书，IoT Edge 运行时会创建临时证书。 但是，这些临时证书将在三个月后过期，并且对于生产方案而言并不安全。 

若要了解设备 CA 证书的作用，请参阅 [Azure IoT Edge 如何使用证书](iot-edge-certs.md)。

有关如何在 IoT Edge 设备上安装证书并从 yaml 文件中引用它们的详细信息，请参阅[在 IoT Edge 设备上安装生产证书](how-to-create-transparent-gateway.md)。 

### <a name="have-a-device-management-plan"></a>创建设备管理计划

在将任何设备投放到生产环境之前，应该知道如何管理将来的更新。 对于 IoT Edge 设备，要更新的组件列表可能包括：

* 设备固件
* 操作系统库
* 容器引擎，例如 Moby
* IoT Edge 守护程序
* CA 证书

有关详细信息，请参阅[更新 IoT Edge 运行时](how-to-update-iot-edge.md)。 更新 IoT Edge 守护程序的当前方法需要通过物理方式或 SSH 访问 IoT Edge 设备。 如果有多个要更新的设备，请考虑将更新步骤添加到脚本或使用 Ansible 等自动化工具。

### <a name="use-moby-as-the-container-engine"></a>使用 Moby 作为容器引擎

容器引擎是任何 IoT Edge 设备的必备组件。 生产环境中仅支持 Moby 引擎。 其他容器引擎（例如 Docker）确实也能在 IoT Edge 上正常运行，但最好是将其用于开发。 与 Azure IoT Edge 配合使用时，可以重新分配 Moby 引擎，Microsoft 将为此引擎提供服务。

### <a name="choose-upstream-protocol"></a>选择上游协议

对于与 IoT 中心的上游通信，可以同时为 IoT Edge 代理和 IoT Edge 中心配置协议（以及使用的端口）。 默认协议为 AMQP，但可以根据网络设置更改协议。 

两个运行时模块都包含 **UpstreamProtocol** 环境变量。 该变量的有效值为： 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

在设备自身上的 yaml 文件中配置 IoT Edge 代理的 UpstreamProtocol 变量。 例如，如果 IoT Edge 设备位于阻止 AMQP 端口的代理服务器后面，则你可能需要将 IoT Edge 代理配置为使用 AMQP over WebSocket （AMQPWS）来建立与 IoT 中心的初始连接。 

IoT Edge 设备建立连接后，请务必在将来的部署中继续为两个运行时模块配置 UpstreamProtocol 变量。 [将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)中提供了此过程的示例。

## <a name="deployment"></a>部署

* **有用提示**
    * 与上游协议保持一致
    * 为系统模块设置主机存储
    * 减少 IoT Edge 集线器使用的内存空间
    * 不要使用模块映像的调试版本

### <a name="be-consistent-with-upstream-protocol"></a>与上游协议保持一致

如果你将 IoT Edge 设备上的 IoT Edge 代理配置为使用不同于默认 AMQP 的协议，则在将来的所有部署中都应声明相同的协议。 例如，如果 IoT Edge 设备位于阻止 AMQP 端口的代理服务器后面，则你可能已将设备配置为通过基于 WebSocket 的 AMQP (AMQPWS) 进行连接。 将模块部署到设备时，为 IoT Edge 代理和 IoT Edge 中心配置相同的 AMQPWS 协议，否则，默认 AMQP 将覆盖设置，并阻止你再次连接。 

只需为 IoT Edge 代理和 IoT Edge 中心模块配置 UpstreamProtocol 环境变量。 其他任何模块将采用运行时模块中设置的任何协议。 

[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)中提供了此过程的示例。

### <a name="set-up-host-storage-for-system-modules"></a>为系统模块设置主机存储

IoT Edge 集线器和代理模块使用本地存储来维护状态，并在模块、设备和云之间启用消息传递。 为了获得更好的可靠性和性能，请将系统模块配置为使用主机文件系统上的存储。

有关详细信息，请参阅[主机存储系统模块](how-to-access-host-storage-from-module.md)。

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>减少 IoT Edge 集线器使用的内存空间

如果要部署的受限制设备具有可用的内存，则可以将 IoT Edge 集线器配置为以更精简的容量运行，并使用较少的磁盘空间。 不过，这些配置会限制 IoT Edge 集线器的性能，因此请找到适合您的解决方案的适当平衡。 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>在受限的设备上不要进行性能优化

默认情况下，IoT Edge 中心已针对性能进行了优化，因此它会尝试分配大块内存。 在 Raspberry Pi 等小型设备上，此配置可能会影响稳定性。 如果要部署具有受约束资源的设备，则可能需要在 IoT Edge 中心将**OptimizeForPerformance**环境变量设置为**false** 。 

如果将**OptimizeForPerformance**设置为**TRUE**，则 MQTT 协议头将使用具有更好的性能的 PooledByteBufferAllocator，但会分配更多的内存。 分配器在32位操作系统或内存不足的设备上不能正常工作。 此外，如果针对性能进行了优化，RocksDb 会为其角色分配更多的内存作为本地存储提供程序。 

有关详细信息，请参阅[资源受限设备的稳定性问题](troubleshoot.md#stability-issues-on-resource-constrained-devices)。

#### <a name="disable-unused-protocols"></a>禁用未使用的协议

若要优化 IoT Edge 集线器的性能并降低内存使用率，另一种方法是关闭不在解决方案中使用的任何协议的协议头。 

通过在部署清单中设置 IoT Edge 中心模块的布尔环境变量来配置协议头。 三个变量如下：

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

所有三个变量都带有两条下划线，可设置为 true 或 false。 

#### <a name="reduce-storage-time-for-messages"></a>减少消息的存储时间

如果由于任何原因无法将消息传送到 IoT 中心，IoT Edge 中心模块会暂时存储消息。 可以配置在使消息过期之前，IoT Edge 集线器在未传递的消息上保留多长时间。 如果设备上存在内存问题，可以降低 IoT Edge 中心模块克隆中的**timeToLiveSecs**值。 

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

标记是一个 docker 概念，可用于区分 docker 容器的各个版本。 标记是附加在容器存储库末尾的后缀（如 **1.0**）。 例如 **mcr.microsoft.com/azureiotedge-agent:1.0**。 标记是可变的，随时可能更改为指向另一容器，因此，团队应该议定一种约定，以便今后在更新模块映像时遵循。 

标记还可帮助你针对 IoT Edge 设备强制实施更新。 将模块的更新版本推送到容器注册表时，请递增标记。 然后，使用递增的标记将新部署推送到设备。 容器引擎将递增的标记识别为新版本，并将最新模块版本提取到设备。 

有关标记约定的示例，请参阅[更新 IoT Edge 运行时](how-to-update-iot-edge.md#understand-iot-edge-tags)，了解 IoT Edge 如何使用滚动更新标记和特定标记来跟踪版本。 

## <a name="networking"></a>联网

* **有用提示**
    * 检查出站/入站配置
    * 允许来自 IoT Edge 设备的连接
    * 配置为通过代理进行通信

### <a name="review-outboundinbound-configuration"></a>检查出站/入站配置

Azure IoT 中心与 IoT Edge 之间的信道始终配置为出站。 对于大多数 IoT Edge 方案，只需建立三个连接。 容器引擎需要连接到保存模块映像的一个或多个容器注册表。 IoT Edge 运行时需要连接到 IoT 中心，以检索设备配置信息，以及发送消息和遥测数据。 如果使用自动预配，则 IoT Edge 守护程序需要连接到设备预配服务。 有关详细信息，请参阅[防火墙和端口配置规则](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment)。

### <a name="allow-connections-from-iot-edge-devices"></a>允许来自 IoT Edge 设备的连接

如果网络设置要求明确允许从 IoT Edge 设备进行的连接，请查看以下 IoT Edge 组件列表：

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
   | \*.azurecr.io | 443 | 个人和第三方容器注册表 |
   | \*.blob.core.windows.net | 443 | 从 blob 存储下载 Azure 容器注册表映像增量  | 
   | \*.azure-devices.net | 5671、8883、443 | IoT 中心访问 |
   | \*.docker.io  | 443 | Docker 中心访问（可选） |

其中的某些防火墙规则继承自 Azure 容器注册表。 有关详细信息，请参阅[配置规则以访问防火墙后面的 Azure 容器注册表](../container-registry/container-registry-firewall-access-rules.md)。

### <a name="configure-communication-through-a-proxy"></a>配置为通过代理进行通信

如果要在使用代理服务器的网络中部署设备，这些设备需要能够通过该代理进行通信，这样才能访问 IoT 中心和容器注册表。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。

## <a name="solution-management"></a>解决方案管理

* **有用提示**
    * 设置日志和诊断
    * 考虑测试和 CI/CD 管道

### <a name="set-up-logs-and-diagnostics"></a>设置日志和诊断

在 Linux 上，IoT Edge daemon 使用日记作为默认日志记录驱动程序。 可以使用命令行工具 `journalctl` 查询守护程序日志。 在 Windows 上，IoT Edge 守护程序使用 PowerShell 诊断。 使用 `Get-IoTEdgeLog` 可以查询守护程序的日志。 IoT Edge 模块使用 JSON 驱动程序进行日志记录，这是默认设置。  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

测试 IoT Edge 部署时，通常可以访问设备来检索日志和进行故障排除。 在部署方案中，可能做不到这一点。 考虑如何收集有关生产环境中设备的信息。 一种做法是使用日志记录模块从其他模块收集信息，然后将其发送到云中。 日志记录模块的一个示例是 [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics)，你也可以设计自己的模块。 

### <a name="place-limits-on-log-size"></a>对日志大小施加限制

默认情况下，小鲸鱼容器引擎不设置容器日志大小限制。 随着时间的推移，这可能会导致设备填满日志并耗尽磁盘空间。 请考虑以下选项以防止出现这种情况：

**选项：设置适用于所有容器模块的全局限制**

可以在容器引擎日志选项中限制所有容器日志记录的大小。 下面的示例将日志驱动程序设置为 `json-file` （推荐），并限制文件的大小和数量：

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

将此信息添加（或追加）到名为 `daemon.json` 的文件，并将其放置在设备平台的适当位置。

| 平台 | 位置 |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

要使更改生效，必须重新启动容器引擎。

**选项：调整每个容器模块的日志设置**

可以在每个模块的**createOptions**中执行此操作。 例如：

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

**Linux 系统上的其他选项**

* 通过将 "`journald`" 设置为默认的日志记录驱动程序，将容器引擎配置为将日志发送到 `systemd`[日志](https://docs.docker.com/config/containers/logging/journald/)。 

* 通过安装 logrotate 工具，定期从设备中删除旧日志。 使用以下文件规范： 

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
