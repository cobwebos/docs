---
title: Azure IoT 中心的高可用性和灾难恢复 | Microsoft Docs
description: 介绍了Azure 和 IoT 中心功能，这些功能有助于构建带灾难恢复功能的 Azure IoT 高可用性解决方案。
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: rkmanda
ms.openlocfilehash: 22c53a85d46cbcb1ed9592d7373ea044e8910297
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248292"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT 中心高可用性和灾难恢复
作为实施弹性 IoT 解决方案的第一步，架构师、开发人员和企业主必须定义要构建的解决方案的运行时间目标。 可以主要根据每个方案的具体业务目标定义这些目标。 针对这种环境，[Azure 业务连续性技术指南]( https://docs.microsoft.com/azure/architecture/resiliency/)一文介绍了一个常规框架来帮助你思考业务连续性和灾难恢复。 [Azure 应用程序的灾难恢复和高可用性](_https://msdn.microsoft.com/library/dn251004.aspx)一文针对 Azure 应用程序的高可用性 (HA) 和灾难恢复 (DR) 实现策略提供了体系结构指导。 

本文介绍 IoT 中心服务专门提供的 HA 和 DR 功能。 从广义上讲，本文讨论的领域包括 

- 区域内部 HA
- 跨区域 DR
- 实现跨区域 HA

根据你为 IoT 解决方案定义的运行时间目标，应确定下面所述的哪些选项最适合业务目标。 将其中的任何 HA/DR 备选方案整合到 IoT 解决方案需要仔细权衡以下方面的利弊：
- 所需的复原能力级别 
- 实施和维护的复杂性
- COGS 影响


## <a name="intra-region-ha"></a>区域内部 HA
IoT 中心服务通过在几乎所有服务层中实现冗余来提供区域内部 HA。 [IoT 中心服务发布的 SLA](_https://azure.microsoft.com/support/legal/sla/iot-hub) 是利用这些冗余实现的。 IoT 解决方案开发人员无需完成任何额外工作就能利用这些 HA 功能。 尽管 IoT 中心提供相当高的运行时间保证，但与任何分布式计算平台一样，暂时性的故障仍有可能出现。 如果刚刚开始将解决方案从本地解决方案迁移到云，则需要将重心从优化“平均故障时间”改为优化“平均恢复时间”。 换而言之，以混合模式操作云时，暂时性故障被视为正常。 必须在与云应用程序交互的组件中内置[重试策略](_https://channel9.msdn.com/Shows/Internet-of-Things-Show/Retry-logic-in-device-SDKs-for-Azure-IoT-Hub)，以处理暂时性故障。 

> [!NOTE]
> 某些 Azure 服务还通过与[可用性区域 (AZ)](_https://docs.microsoft.com/azure/availability-zones/az-overview) 集成，在区域中提供附加的可用性层。 IoT 中心服务目前不支持 AZ。

## <a name="cross-region-dr"></a>跨区域 DR
在极少见的情况下，电源故障或其他涉及到实物资产的故障会导致数据中心遇到长时间的服务中断。 此类事件非常罕见，在此期间，上述区域内部 HA 不一定总能发挥作用。 IoT 中心提供多种解决方案，用于在发生此类长时间服务中断后进行恢复。 出现此类情况时为客户提供的恢复选项包括“Microsoft 发起的故障转移”和“手动故障转移”。 两者之间的根本差别在于，前者由 Microsoft 发起，后者由用户发起。 此外，与 Microsoft 发起的故障转移选项相比，手动故障转移提供更低的恢复时间目标 (RTO)。 以下部分讨论了每个选项提供的具体 RTO。 执行上述任一选项从主要区域故障转移 IoT 中心时，中心将在对应的 [Azure 异地配对区域](_https://docs.microsoft.com/azure/best-practices-availability-paired-regions)完全正常运行。 


这两个故障转移选项提供以下恢复点目标 (RPO)：

| 数据类型 | 恢复点目标 (RPO) |
| --- | --- |
| 标识注册表 |丢失 0-5 分钟的数据 |
| 设备孪生数据 |丢失 0-5 分钟的数据 |
| 云到设备的消息** |丢失 0-5 分钟的数据 |
| 父作业**和设备作业 |丢失 0-5 分钟的数据 |
| 设备到云的消息 |所有未读的消息都丢失 |
| 操作监视消息 |所有未读的消息都丢失 |
| 云到设备的反馈消息 |所有未读的消息都丢失 |

完成 IoT 中心的故障转移操作后，来自设备和后端应用程序的所有操作预期可继续进行，无需人工干预。

> [!CAUTION]
> - 故障转移后，IoT 中心内置事件终结点的事件中心兼容名称和终结点会发生变化。 使用事件中心客户端或事件处理程序主机从内置终结点接收遥测消息时，应[使用 IoT 中心连接字符串](_https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin#read-from-the-built-in-endpoint)建立连接。 这可以确保在故障转移后，后端应用程序可继续工作，而无需人工干预。 如果在后端应用程序中直接使用事件中心兼容的名称和终结点，在故障转移后需要通过[提取新的事件中心兼容名称和终结点](_https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin#read-from-the-built-in-endpoint)来重新配置应用程序，这样才能继续操作。 
>
> - 故障转移后，可以借助前面配置的相同订阅来使用通过事件网格发出的事件，前提是这些事件网格订阅仍然可用。
>
> - **在此功能的预览版中，手动故障转移期间无法恢复云到设备的消息和父作业。

### <a name="microsoft-initiated-failover"></a>Microsoft 发起的故障转移
在极其罕见的情况下，Microsoft 会执行 Microsoft 发起的故障转移，以将所有 IoT 中心从受影响的区域故障转移到对应的异地配对区域。 这是默认选项（用户无法选择禁用），且无需用户的干预。 Microsoft 有权决定何时执行此选项。 在故障转移用户的中心之前，此机制不要求用户许可。 Microsoft 发起的故障转移的恢复时间目标 (RTO) 为 2 到 26 小时。 RTO 较高的原因是，Microsoft 必须代表该区域中所有受影响的客户执行故障转移操作。 如果运行的某个较不关键 IoT 解决方案可以承受大约一天时间的停机，则可以依赖此选项来满足 IoT 解决方案的总体灾难恢复目标。 “恢复时间”部分介绍了触发此过程后，使运行时操作完全正常所需的总时间。 

### <a name="manual-failover-preview"></a>手动故障转移（预览版）

如果 Microsoft 发起的故障转移的 RTO 无法满足企业的正常运行时间目标，应考虑使用手动故障转移来自行触发故障转移过程。 此选项的 RTO 大致在 10 分钟到几个小时。 目前，RTO 取决于针对故障转移的 IoT 中心实例注册的设备数。 托管大约 100,000 台设备的中心的 RTO 大致是 15 分钟。 “恢复时间”部分介绍了触发此过程后，使运行时操作完全正常所需的总时间。

不管主要区域是否遇到停机，手动故障转移选项始终可用。 因此，用户可能会使用此选项来执行计划内故障转移。 计划内故障转移的一个示例用途是执行定期的故障转移演练。 需要注意的是，计划内故障转移操作会导致中心在此选项的 RTO 定义的时间段内停机，同时会导致数据丢失（由上面的 RPO 表定义）。 可以考虑设置一个测试 IoT 中心实例来定期执行计划内故障转移选项，以便在发生实际灾难时，自信地让端到端解决方案正常运行。 

> [!IMPORTANT]
> - 不应针对生产环境中使用的 IoT 中心执行测试演练。 
>
> - 不应使用手动故障转移作为在 Azure 异地配对区域之间永久迁移中心的机制。 否则，会增大从驻留在旧主要区域中的设备针对中心执行的操作的延迟。 
>
> - 手动故障转移目前以预览版提供，在以下 Azure 区域不可用。 美国东部、美国西部、北欧、西欧、巴西南部、美国中南部。

### <a name="failback"></a>故障回复

再次触发故障转移操作即可故障回复到旧的主要区域。 如果执行了原始故障转移操作，以便在原始主要区域发生长时间服务中断后进行恢复，我们建议在原始位置从服务中断恢复后，将中心故障回复到原始位置。 

> [!IMPORTANT]
> - 每天只允许用户执行 2 次成功的故障转移和 2 次成功的故障回复操作。
>
> - 不允许背靠背（连续）的故障转移/故障回复操作。 每次执行此类操作后，用户必须等待 1 小时。

### <a name="time-to-recover"></a>恢复时间

尽管 IoT 中心实例的 FQDN（因此也包括连接字符串）在故障转移后保持相同，但基础 IP 地址会发生变化。 因此，可使用以下函数来表示触发故障转移过程后，针对 IoT 中心实例执行的运行时操作完全正常所需的总时间。

恢复时间 = RTO [手动故障转移为 10 分钟到 2 小时 | Microsoft 发起的故障转移为 2 到 26 小时] + DNS 传播延迟 + 客户端应用程序刷新任何缓存 IoT 中心 IP 地址花费的时间。 

> [!IMPORTANT]
> IoT SDK 不会缓存 IoT 中心的 IP 地址。 我们建议，与 SDK 交互的用户代码不应缓存 IoT 中心的 IP 地址。 

## <a name="achieve-cross-region-ha"></a>实现跨区域 HA
如果 Microsoft 发起的故障转移或手动故障转移选项提供的 RTO 无法满足企业的运行时间目标，应考虑在每个设备上实施自动跨区域故障转移机制。 IoT 解决方案中对部署拓扑的完整处理不在本文的介绍范围内。 本文将讨论用于提供高可用性和灾难恢复功能的*区域性故障转移*部署模型。

在区域故障转移模型中，解决方案后端主要在一个数据中心位置运行。 辅助 IoT 中心和后端部署在另一个数据中心位置。 如果主要区域中的 IoT 中心遭遇服务中断或者从设备到主要区域的网络连接中断，设备将使用辅助服务终结点。 可以通过实现跨区域故障转移模型而不是保留在单个区域中来提高解决方案可用性。 

概括而言，为了实现 IoT 中心的区域故障转移模型，需要执行以下步骤：

* **辅助 IoT 中心和设备路由逻辑**：如果主要区域的服务中断，设备必须开始连接到次要区域。 由于大多数服务状态感知的性质，解决方案管理员通常触发区域间的故障转移过程。 要使新终结点与设备通信，同时保留过程控制权，最佳方式是让它们定期在*监护*服务中检查是否存在当前活动的终结点。 该监护服务可以是 Web 应用程序，可使用 DNS 重定向技术将它复制并使其可访问（例如，使用 [Azure 流量管理器][Azure Traffic Manager]）。

   > [!NOTE]
   > IoT 中心服务不是 Azure 流量管理器中受支持的终结点类型。 我们建议在提议的监护服务中实现终结点运行状况探测 API，使之与 Azure 流量管理器集成。

* **标识注册表复制**：为了可用，辅助 IoT 中心必须包含能够连接到解决方案的所有设备标识。 解决方案应该保留设备标识的异地复制备份，并在切换设备的活动终结点之前将其上传到辅助 IoT 中心。 IoT 中心的设备标识导出功能在此背景下非常有用。 有关详细信息，请参阅 [IoT 中心开发人员指南 - 标识注册表][IoT Hub developer guide - identity registry]。
* **合并逻辑**：当主要区域再次可供使用时，已在辅助站点中创建的所有状态和数据都必须迁移回主要区域。 此状态和数据主要与设备标识和应用程序元数据相关，必须与主要 IoT 中心以及主要区域中的任何其他应用程序特定存储合并。 为简化此步骤，应当使用幂等操作。 幂等操作可以将副作用降到最低，包括来自最终一致的事件分布的副作用，以及来自事件的重复项目或失序传送的副作用。 此外，应用程序逻辑应该设计为能够容许潜在的不一致或稍微过期的状态。 之所以发生此情况是因为系统需要额外的时间来根据恢复点目标 (RPO) 修复自身。

## <a name="choose-the-right-hadr-option"></a>选择适当的 HA/DR 选项
下面汇总了本文所述的 HA/DR 选项，可将其用作参考框架来选择适用于解决方案的选项

| HA/DR 选项 | RTO | RPO | 是否需要人工干预？ | 实施复杂性 | 附加成本影响|
| --- | --- | --- | --- | --- | --- | --- |
| Microsoft 发起的故障转移 |2 - 26 小时|参考上面的 RPO 表|否|无|无
| 手动故障转移 |10 分钟 - 2 小时|参考上面的 RPO 表|是|极低。 只需从门户触发此操作。|无
| 跨区域 HA |小于 1 分钟|取决于自定义 HA 解决方案的复制频率|否|高|超过 1 个 IoT 中心的 1 倍 

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure IoT 中心的详细信息，请参阅以下链接：

* [IoT 中心入门（教程）][lnk-get-started]
* [什么是 Azure IoT 中心？][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[What is Azure IoT Hub?]: about-iot-hub.md
