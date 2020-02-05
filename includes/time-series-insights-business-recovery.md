---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 6a3837d01815306e469a684404ab76506f547f43
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "77013726"
---
## <a name="business-disaster-recovery"></a>业务灾难恢复

本部分介绍 Azure 时序见解的功能，这些功能使应用和服务保持运行状态（即使发生灾难，也称为*业务灾难恢复*）。

### <a name="high-availability"></a>高可用性

作为 Azure 服务，时序见解通过在 Azure 区域级别使用冗余来提供某些*高可用性*功能。 例如，Azure 通过 Azure 的*跨区域可用性*功能支持灾难恢复功能。

通过 Azure 提供的其他高可用性功能（也可供任何时序见解实例使用）包括：

- **故障转移**： Azure 提供[异地复制和负载均衡](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)。
- **数据还原**和**存储恢复**： Azure 提供[多个选项用于保留和恢复数据](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)。
- **Azure Site Recovery**： Azure 通过[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/)提供 Site Recovery 功能。
- **Azure 备份**： [Azure 备份](https://docs.microsoft.com/azure/backup/backup-architecture)支持 azure vm 的本地和云中备份。

请确保启用相关的 Azure 功能，为你的设备和用户提供全局跨区域的高可用性。

> [!NOTE]
> 如果 Azure 配置为启用跨区域可用性，Azure 时序见解中不需要其他跨区域可用性配置。

### <a name="iot-and-event-hubs"></a>IoT 和事件中心

某些 Azure IoT 服务还包括内置的业务灾难恢复功能：

- [Azure IoT 中心高可用性灾难恢复](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)，包括区域内冗余
- [Azure 事件中心策略](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure 存储冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

将时序见解与其他服务集成可提供额外的灾难恢复机会。 例如，发送到事件中心的遥测数据可能保存到备份 Azure Blob 存储数据库。

### <a name="time-series-insights"></a>时序见解

可以通过多种方式来使时序见解数据、应用和服务保持运行状态，即使它们中断也是如此。 

但是，你可能还需要确定 Azure 时序环境的完整备份副本，目的如下：

- 作为*故障转移实例*，用于将数据和流量重定向到的时序见解
- 保留数据和审核信息

通常，复制时序见解环境的最佳方式是在备份 Azure 区域中创建第二个时序见解环境。 事件还会从主事件源发送到此辅助环境。 请确保使用第二个专用使用者组。 按照前面所述，遵循该源的业务灾难恢复指导原则。

若要创建重复的环境：

1. 在另一个区域中创建环境。 有关详细信息，请参阅[在 Azure 门户中创建新的时序见解环境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)。
1. 为事件源创建另一个专用使用者组。
1. 将该事件源连接到新环境。 请确保指定第二个专用使用者组。
1. 查看时序见解[IoT 中心](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)和[事件中心](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)文档。

如果发生事件：

1. 如果主要区域在灾难事件期间受到影响，请将操作重新路由到备用时序见解环境。
1. 使用第二个区域备份和恢复所有时序见解遥测和查询数据。

> [!IMPORTANT]
> 如果发生故障转移：
> 
> * 也可能发生延迟。
> * 可能会发生消息处理中的瞬间峰值，因为操作会重新路由。
> 
> 有关详细信息，请参阅[缓解时序见解中的延迟](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)。

