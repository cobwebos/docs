---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 07/09/2020
ms.openlocfilehash: de5d3f8f32e928c77ffd6028ec764793ab7229ac
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495325"
---
## <a name="business-disaster-recovery"></a>业务灾难恢复

本部分介绍即使发生了灾难，也能使应用和服务保持正常运行的 Azure 时序见解功能（称为“业务灾难恢复”）。 

### <a name="high-availability"></a>高可用性

作为 Azure 服务，Azure 时序见解通过在 Azure 区域级别使用冗余来提供某些*高可用性*功能。 例如，Azure 支持通过其跨区域可用性功能来实现 灾难恢复功能。 

通过 Azure 提供的其他高可用性功能（也可供任何 Azure 时序见解实例使用）包括：

- **故障转移**：Azure 提供[异地复制和负载均衡](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)。
- **数据恢复**和**存储恢复**：Azure 提供[多个用于保留和恢复数据的选项](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)。
- **Azure Site Recovery**： Azure 通过[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/)提供 Site Recovery 功能。
- **Azure 备份**： [Azure 备份](https://docs.microsoft.com/azure/backup/backup-architecture)支持 azure vm 的本地和云中备份。

请务必启用相关的 Azure 功能，以便为设备和用户提供全局跨区域高可用性。

> [!NOTE]
> 如果已将 Azure 配置为启用跨区域可用性，则不需要在 Azure 时序见解中采用其他跨区域可用性配置。

### <a name="iot-and-event-hubs"></a>IoT 和事件中心

某些 Azure IoT 服务也包含内置的业务灾难恢复功能：

- [Azure IoT 中心高可用性灾难恢复](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)，包括区域内冗余
- [Azure 事件中心策略](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure 存储冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

将 Azure 时序见解与其他服务集成可提供额外的灾难恢复机会。 例如，可将发送到事件中心的遥测数据保留在 Azure Blob 存储备份数据库中。

### <a name="azure-time-series-insights"></a>Azure 时序见解

可以通过多种方式将 Azure 时序见解数据、应用和服务保持运行状态，即使它们中断也是如此。 

但是，还可以确定是否需要出于以下目的，来创建 Azure 时序环境的完整备份副本：

- 专用于 Azure 时序见解的*故障转移实例*，用于将数据和流量重定向到
- 保留数据和审核信息

通常，复制 Azure 时序见解环境的最佳方式是在备份 Azure 区域中创建另一个 Azure 时序见解环境。 来自主要事件源的事件也会发送到此辅助环境。 请确保使用第二个专用使用者组。 遵循前面所述的源业务灾难恢复指导原则。

若要创建副本环境：

1. 在另一个区域中创建环境。 有关详细信息，请参阅[在 Azure 门户中创建新的 Azure 时序见解环境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)。
1. 为事件源创建另一个专用使用者组。
1. 将该事件源连接到新环境。 请确保指定第二个专用使用者组。
1. 查看 Azure 时序见解[IoT 中心](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)和[事件中心](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)文档。

发生事件时：

1. 如果主要区域在发生灾难事件时受到影响，请将操作重新路由到备份 Azure 时序见解环境。
1. 使用第二个区域备份和恢复所有 Azure 时序见解遥测和查询数据。

> [!IMPORTANT]
> 发生故障转移时：
> 
> * 也可能会发生延迟。
> * 由于需要重新路由操作，还可能会出现短暂的消息处理高峰。
> 
> 有关详细信息，请参阅[减少 Azure 时序见解中的延迟](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)。

