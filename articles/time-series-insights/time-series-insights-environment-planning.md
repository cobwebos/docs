---
title: 计划 Azure 时序见解环境的缩放 | Microsoft Docs
description: 本文介绍如何规划 Azure 时序见解环境时遵循最佳做法。 涵盖的方面包括存储容量、 数据保留、 入口容量、 监视和业务连续性和灾难恢复 (BCDR)。
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 9d9f9b7fe7aafa927f023e1d4e30e079a26f7fab
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431054"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>规划 Azure 时间系列 Insights GA 环境

本文介绍如何规划 Azure 时序见解公开上市 (GA）) 环境基于预期的入口速率和数据保留要求。

## <a name="video"></a>视频

**观看此视频，了解有关在 Azure 时序见解和规划它的数据保留期的详细信息**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>最佳做法

若要开始使用时序见解，最好是如果您知道想要按分钟，并且你需要将数据存储的长推送的数据量。  

有关时序见解 SKU 的容量和保留期的详细信息，请参阅[时序见解定价](https://azure.microsoft.com/pricing/details/time-series-insights/)。

若要最规划长期成功的时序见解环境，请考虑以下属性：

- <a href="#storage-capacity">存储容量</a>
- <a href="#data-retention">数据保留期</a>
- <a href="#ingress-capacity">入口容量</a>
- <a href="#shape-your-events">调整您的事件</a>
- <a href="#ensure-that-you-have-reference-data">确保就地具有引用数据</a>

## <a name="storage-capacity"></a>存储容量

默认情况下，时序见解会将数据保留基于预配的存储量 (单位&#215;的每个单位的存储量) 和入口。

## <a name="data-retention"></a>数据保留

您可以更改**数据保留时间**在时序见解环境中设置。 你可以启用最长 400 天的保留期。 

时序见解有两种模式。 为确保您的环境具有最新的数据进行优化的一种模式。 此模式下，默认情况下处于打开状态。 

为确保满足保留期限制，另一种模式进行优化。 在第二个模式下，如果满足该环境的整体存储容量，则暂停流入。 

您可以调整保留期和 Azure 门户中的环境的配置页上的两种模式之间进行切换。

可在时序见解环境中配置最长 400 天的数据保留。

### <a name="configure-data-retention"></a>配置数据保留

1. 在 [Azure 门户](https://portal.azure.com)中，选择时序见解环境。

1. 在中**时序见解环境**窗格下**设置**，选择**配置**。

1. 在中**数据保留时间 （以天为单位）** 框中，输入 1 到 400 之间的值。

   [![配置保留期](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> 若要了解有关如何实施适当的数据保留策略的详细信息，请参阅[如何配置保留期](./time-series-insights-how-to-configure-retention.md)。

## <a name="ingress-capacity"></a>入口容量

若要专注于为规划时序见解环境的第二个区域是入口容量。 入口容量是每分钟分配的派生。

从限制的角度看的数据包大小为 32 KB 的入口数据包视为 32 事件，每个 1 KB 的大小。 允许的最大事件大小为 32 KB。 大小大于 32 KB 的数据包将被截断。

下表总结了每个单位的每个时序见解 SKU 的入口容量：

|SKU  |每月的事件计数  |每月的事件大小  |每分钟的事件计数  |每分钟的事件大小  |
|---------|---------|---------|---------|---------|
|S1     |   3000 万     |  30 GB     |  720    |  720 KB   |
|S2     |   3 亿    |   300 GB   | 7,200   | 7,200 KB  |

可以在单一环境中，将 S1 或 S2 SKU 的容量增加到 10 个单位。 您不能从 S1 环境迁移到 S2。 不能从 S2 环境迁移到 S1。

对于入口容量，首先要确定所需的每月按总入口。 接下来，确定你每分钟需求是什么。 

限制和延迟每分钟容量中扮演角色。 如果数据入口中数据的持续时间少于 24 小时激增时序见解可以"赶上"两次的速率上表中列出的入口速率。

例如，如果你有一个 S1 SKU，则传入数据的每分钟、 720 事件速率和数据速率小于或等于 1440 事件的速率的时间少于 1 小时的高峰，则没有明显的延迟在环境中。 但是，如果超过 1,440 每分钟超过一小时的事件，您可能会遇到数据可视化，可用于您的环境中的查询的延迟时间。

您可能不知道想要推送提前多少数据。 在这种情况下，可以查找数据的遥测数据[Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)并[Azure 事件中心](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/)门户在 Azure 订阅中。 遥测可有助于确定如何预配环境。 使用**指标**要查看其遥测数据的相应的事件源在 Azure 门户中的窗格。 了解事件源指标后，可以更有效地计划和预配时序见解环境。

### <a name="calculate-ingress-requirements"></a>计算入口需求

若要计算入口需求：

- 确认入口容量高于平均的每分钟费率和你的环境足够大，能够处理预期的入口等效于容量不超过一小时的两倍。

- 如果入口激增用于上次的时间超过 1 小时，激增速率作为平均值。 预配容量足以处理激增速率的环境。

### <a name="mitigate-throttling-and-latency"></a>缓解限制和延迟

有关如何避免限制和延迟的信息，请参阅[缓解限制和延迟](time-series-insights-environment-mitigate-latency.md)。

## <a name="shape-your-events"></a>塑造事件

请务必确保发送到时序见解事件支持在预配的环境的大小的方式。 （相反，可以映射到多少个事件读取时序见解环境的大小和每个事件的大小。）还有一点需要考虑一下您可能想要使用进行切片和筛选依据的属性时查询数据。

> [!TIP]
> 查看 JSON 成型文档中的[将事件发送](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events)。

## <a name="ensure-that-you-have-reference-data"></a>确保有引用数据

一个*引用数据集*是增加事件源中的事件的项的集合。 时序见解入口引擎将事件源与相应的数据行中的每个事件联接引用数据集内。 增强的事件就可用于查询。 联接基于**Primary Key**引用数据集内定义的列。

> [!NOTE]
> 引用数据不以追溯方式进行联接。 仅当前和将来的入口数据匹配，并在已配置和上传后联接到引用数据集。 如果你打算将大量的历史数据发送到时序见解和不第一次上载或创建时序见解引用数据，您可能必须重做你的工作 (提示： 并不有趣)。  

若要了解有关如何创建、 上载和管理时序见解中的引用数据的详细信息，请参阅我们[参考数据集文档](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>后续步骤

- 开始创建[在 Azure 门户中的新时序见解环境](time-series-insights-get-started.md)。

- 了解如何[添加事件中心事件源](time-series-insights-how-to-add-an-event-source-eventhub.md)向时序见解。

- 阅读有关如何[配置 IoT 中心事件源](time-series-insights-how-to-add-an-event-source-iothub.md)。
