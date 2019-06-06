---
ms.topic: include
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: 8a3c630b54ff95a9b1200e2421c787a514a0aa52
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431038"
---
## <a name="business-disaster-recovery"></a>业务灾难恢复

本部分介绍 Azure 时序见解，使应用和服务运行，即使发生灾难的功能 (称为*业务灾难恢复*)。

### <a name="high-availability"></a>高可用性

作为一项 Azure 服务，时序见解提供某些*高可用性*按 Azure 区域级别使用冗余的功能。 例如，Azure 支持通过 Azure 的灾难恢复功能*跨区域可用性*功能。

通过 Azure （和还可用于任何时序见解实例） 提供的其他高可用性功能包括：

- **故障转移**：Azure 提供了[异地复制和负载平衡](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)。
- **数据恢复**并**存储恢复**:Azure 提供了[多个选项来保留和恢复数据](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)。
- **站点恢复**:Azure 提供站点恢复功能，通过[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/)。

请确保启用了相关的 Azure 功能为你的设备和用户提供全局跨区域高可用性。

> [!NOTE]
> 如果 Azure 配置为启用跨区域可用性，Azure 时序见解中不需要任何其他跨区域可用性配置。

### <a name="iot-and-event-hubs"></a>IoT 和事件中心

某些 Azure IoT 服务还包括内置业务灾难恢复功能：

- [IoT 中心高可用性灾难恢复](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)，包括内部区域冗余
- [事件中心策略](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure 存储冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

与其他服务集成时序见解提供附加的灾难恢复的机会。 例如，发送到事件中心的遥测数据可能会保留到 Azure Blob 存储数据库的备份。

### <a name="time-series-insights"></a>时序见解

有几种方法来保留时序见解数据、 应用和服务运行，即使它们中断。 

但是，您可能确定 Azure 时序环境的完整备份副本也是必需的出于以下目的：

- 作为*故障转移实例*专门用于时序见解数据重定向和流量
- 若要保留数据和审核信息

一般情况下，重复的时序见解环境的最佳方法是在备份 Azure 区域中创建第二个时序见解环境。 事件是从您的主要事件源也发送到此辅助环境。 请确保使用第二个、 专用使用者组。 按照该源业务灾难恢复指南，如前面所述。

若要创建重复的环境：

1. 在另一个区域中创建环境。 有关详细信息，请参阅[在 Azure 门户中创建新的时序见解环境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)。
1. 为事件源创建另一个专用使用者组。
1. 将该事件源连接到新环境。 请确保指定的第二个、 专用使用者组。
1. 查看时序见解[IoT 中心](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)并[事件中心](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)文档。

如果发生事件：

1. 如果主要区域在灾难事件期间受到影响，请将操作重新路由到备用时序见解环境。
1. 使用第二个区域备份和恢复所有时序见解遥测数据和查询数据。

> [!IMPORTANT]
> 如果发生故障转移：
> 
> * 也可能发生延迟。
> * 消息处理，瞬间出现峰值可能会出现，如重新路由操作。
> 
> 有关详细信息，请参阅[缓解时序见解中的延迟](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)。

