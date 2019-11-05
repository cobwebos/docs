---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/09/2019
ms.openlocfilehash: 038d25a01c02799f48b8430fb6ac3facc1cd3bd6
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990878"
---
## <a name="business-disaster-recovery"></a>业务灾难恢复

本部分介绍即使发生了灾难，也能使应用和服务保持正常运行的 Azure 时序见解功能（称为“业务灾难恢复”）。

### <a name="high-availability"></a>高可用性

作为一项 Azure 服务，时序见解使用 Azure 区域级别的冗余提供某些高可用性功能。 例如，Azure 支持通过其跨区域可用性功能来实现 灾难恢复功能。

通过 Azure 提供的其他高可用性功能（以及同样适用于任何时序见解实例的功能）包括：

- **故障转移**： Azure 提供[异地复制和负载均衡](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)。
- **数据还原**和**存储恢复**： Azure 提供[多个选项用于保留和恢复数据](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)。
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

将时序见解与其他服务集成有可能会提供更多的灾难恢复机制。 例如，可将发送到事件中心的遥测数据保留在 Azure Blob 存储备份数据库中。

### <a name="time-series-insights"></a>时序见解

可通过多种方式使时序见解数据、应用和服务保持正常运行，即使发生中断。 

但是，还可以确定是否需要出于以下目的，来创建 Azure 时序环境的完整备份副本：

- 时序见解专门将数据和流量重定向到某个故障转移实例
- 保留数据和审核信息

一般而言，复制时序见解环境的最佳方法是在备份 Azure 区域中创建另一个时序见解环境。 来自主要事件源的事件也会发送到此辅助环境。 请确保使用第二个专用使用者组。 遵循前面所述的源业务灾难恢复指导原则。

若要创建副本环境：

1. 在另一个区域中创建环境。 有关详细信息，请参阅[在 Azure 门户中创建新的时序见解环境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)。
1. 为事件源创建另一个专用使用者组。
1. 将该事件源连接到新环境。 请确保指定第二个专用使用者组。
1. 查看时序见解 [IoT 中心](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)和[事件中心](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)文档。

发生事件时：

1. 如果主要区域在灾难事件期间受到影响，请将操作重新路由到备用时序见解环境。
1. 使用第二个区域来备份和恢复所有时序见解遥测数据与查询数据。

> [!IMPORTANT]
> 发生故障转移时：
> 
> * 也可能会发生延迟。
> * 由于需要重新路由操作，还可能会出现短暂的消息处理高峰。
> 
> 有关详细信息，请参阅[缓解时序见解中的延迟](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)。

