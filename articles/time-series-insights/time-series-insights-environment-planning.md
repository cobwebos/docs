---
title: 规划 GA 环境-Azure 时序见解 |Microsoft Docs
description: 了解在规划 GA 环境时应遵循的最佳做法。
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 2dd3b79e931464e83264433a923e9078b2f62525
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006951"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>规划 Azure 时序见解正式版环境

本文介绍如何根据预期流入速率和数据保留要求规划 Azure 时序见解正式版 (GA) 环境。

## <a name="video"></a>视频

**观看此视频，了解有关 Azure 时序见解中的数据保留的详细信息，以及如何对其进行规划**：<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>最佳实践

若要开始使用 Azure 时序见解，最好知道要在一分钟内推送的数据量以及存储数据所需的时间。  

有关时序见解 SKU 的容量和保留期的详细信息，请参阅[时序见解定价](https://azure.microsoft.com/pricing/details/time-series-insights/)。

若要最合理地规划时序见解环境以取得长期成功，请考虑以下属性：

- [存储容量](#storage-capacity)
- [数据保留期](#data-retention)
- [流入容量](#ingress-capacity)
- [调整事件](#shape-your-events)
- [确保已准备好参考数据](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>存储容量

默认情况下，时序见解根据预配的存储量（单位数乘以每个单位的存储量）和流入量来保留数据。

## <a name="data-retention"></a>数据保留

可以在 Azure 时序见解环境中更改 "**数据保留时间**" 设置。 可以启用最长 400 天的保留期。 

Azure 时序见解具有两种模式：

* 一种模式针对最新数据进行了优化。 它强制执行**清除旧数据**的策略，使实例可以使用最新数据。 此模式默认已启用。 
* 其他模式将优化数据，使其保持低于配置的保留限制。 **暂停流入**防止新数据在选择为“超过存储限制的行为”时被读取。 

可在 Azure 门户的环境配置页中调整保留期并在这两种模式之间切换。

> [!IMPORTANT]
> 可在 Azure 时序见解 GA 环境中配置最长 400 天的数据保留。

### <a name="configure-data-retention"></a>配置数据保留

1. 在 [Azure 门户](https://portal.azure.com)中，选择时序见解环境。

1. 在“时序见解环境”窗格中的“设置”下，选择“配置”。

1. 在“数据保留时间(天)”框中，输入 1 到 400 的值。

   [![配置保留期](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> 若要详细了解如何实施适当的数据保留策略，请参阅[如何配置保留期](./time-series-insights-how-to-configure-retention.md)。

## <a name="ingress-capacity"></a>入口容量

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>环境规划

在规划时序见解环境时，需要重点考虑的第二个方面是流入容量。 流入容量是每分钟分配量的一个衍生属性。

从限制的角度看，大小达到 32 KB 的流入数据包被视为 32 个事件，每个事件的大小为 1 KB。 允许的最大事件为 32 KB。 大于 32 KB 的数据包将被截断。

可以在单一环境中，将 S1 或 S2 SKU 的容量增加到 10 个单位。 无法从 S1 环境迁移到 S2 环境。 无法从 S2 环境迁移到 S1 环境。

对于流入容量，首先应该确定每月所需的流入总量。 接下来，确定每分钟的需求。 

限制和延迟对每分钟容量的影响很大。 如果数据流入量会出现持续 24 小时以下的高峰，则时序见解可以两倍于上表中所列的速度“赶上”流入速率。

例如，如果你使用单个 S1 SKU，流入数据的速率为每分钟 720 个事件，数据流入高峰的速率为 1,440 个事件（或更少）且持续时间不到 1 小时，则环境中不会出现明显的延迟。 但是，如果速率大于每分钟 1,440 个事件且超过 1 小时，则可能会发生数据延迟，这可在环境中查看并查询。

你可能无法提前知道想要推送多少数据。 在这种情况下，可以在 Azure 门户订阅的 [Azure IoT 中心](../iot-hub/iot-hub-metrics.md)和 [Azure 事件中心](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/)查找遥测数据。 这些遥测数据有助于确定如何预配环境。 在 Azure 门户使用相应事件源的“指标”页查看遥测数据。 了解事件源指标后，可以更有效地计划和预配时序见解环境。

### <a name="calculate-ingress-requirements"></a>计算入口需求

若要计算流入要求：

- 确认流入容量高于平均每分钟的速率，并且环境足够大，能够在 1 小时内处理相当于两倍容量的预期流入量。

- 如果发生持续超过 1 小时的流入高峰，请使用高峰速率作为平均值。 使用可以应对高峰速率的容量预配环境。

### <a name="mitigate-throttling-and-latency"></a>缓解限制和延迟

有关如何避免限制和延迟的信息，请参阅[缓解限制和延迟](time-series-insights-environment-mitigate-latency.md)。

## <a name="shape-your-events"></a>塑造事件

必须确保向时序见解发送事件的方式支持预配的环境大小。 （相反，你可以将环境的大小映射到事件时序见解的数量以及每个事件的大小。）还需要考虑在查询数据时可能想要用来进行切片和筛选的属性。

> [!TIP]
> 请查看[发送事件](time-series-insights-send-events.md)中的 JSON 塑形文档。

## <a name="ensure-that-you-have-reference-data"></a>确保已获得参考数据

参考数据集是对来自事件源的事件进行补充的项集合。 时序见解流入引擎将来自事件源的每个事件与参考数据集中的相应数据行联接到一起。 然后可以查询补充后的事件。 该联接基于参考数据集中定义的“主键”列。

> [!NOTE]
> 参考数据不以追溯方式进行联接。 在配置并上传参考数据集后，只会将当前和将来的流入数据与参考数据集相匹配并联接到其中。 如果你打算将大量的历史数据发送到时序见解，但未事先在时序见解中上传或创建参考数据，到时可能需要从头开始，这是一件很麻烦的事。  

若要详细了解如何在时序见解中创建、上传和管理参考数据，请参阅[参考数据集文档](time-series-insights-add-reference-data-set.md)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>后续步骤

- 开始[在 Azure 门户中创建新的时序见解环境](time-series-insights-get-started.md)。

- 了解如何[将事件中心事件源添加](time-series-insights-how-to-add-an-event-source-eventhub.md)到时序见解。

- 了解如何[配置 IoT 中心事件源](time-series-insights-how-to-add-an-event-source-iothub.md)。
