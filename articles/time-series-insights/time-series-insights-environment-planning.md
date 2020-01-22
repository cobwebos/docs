---
title: 规划 GA 环境-Azure 时序见解 |Microsoft Docs
description: 准备、配置和部署 Azure 时序见解 GA 环境的最佳实践。
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 972bb2a804057037deedb448674abafcc175b21f
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314804"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>规划 Azure 时序见解 GA 环境

本文介绍如何根据预期的入口速率和数据保留要求规划 Azure 时序见解正式发行版（GA）环境。

## <a name="video"></a>视频

**观看此视频，了解有关 Azure 时序见解中的数据保留的详细信息，以及如何对其进行规划**：<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>最佳实践

若要开始使用 Azure 时序见解，最好知道要在一分钟内推送的数据量以及存储数据所需的时间。  

有关时序见解 Sku 的容量和保留期的详细信息，请参阅[时序见解定价](https://azure.microsoft.com/pricing/details/time-series-insights/)。

若要最好地计划长期成功的时序见解环境，请考虑以下属性：

- [存储容量](#storage-capacity)
- [数据保持期](#data-retention)
- [入口容量](#ingress-capacity)
- [整理事件](#shape-your-events)
- [确保已准备好引用数据](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>存储容量

默认情况下，时序见解根据预配的存储量（ &#215;每个单位的存储量）和入口保留数据。

## <a name="data-retention"></a>数据保留

可以在 Azure 时序见解环境中更改 "**数据保留时间**" 设置。 最多可启用400天的保留期。 

Azure 时序见解有两种模式：

* 一种模式优化了最新的数据。 它强制实施一个策略，以**清除旧数据**，使其与实例一起使用最新的数据。 此模式默认为启用状态。 
* 其他数据将优化数据，使其保持低于配置的保留限制。 如果将新数据选作**超出存储限制的行为**，**暂停入口**会阻止引入新数据。

可以在 Azure 门户中环境配置页上的两种模式之间进行调整和切换。

> [!IMPORTANT]
> 你可以在 Azure 时序见解 GA 环境中配置最多400天的数据保留期。

### <a name="configure-data-retention"></a>配置数据保留

1. 在 [Azure 门户](https://portal.azure.com)中，选择时序见解环境。

1. 在**时序见解环境**窗格中的 "**设置**" 下，选择 "**存储配置**"。

1. 在 "**数据保留时间（天）** " 框中，输入一个介于1和400之间的值。

   [![配置保留](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> 若要了解有关如何实现适当的数据保留策略的详细信息，请参阅[如何配置保留期](./time-series-insights-how-to-configure-retention.md)。

## <a name="ingress-capacity"></a>入口容量

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>环境规划

规划时序见解环境的第二个领域是入口容量。 入口容量是每分钟分配的衍生量。

从限制的角度来看，数据包大小为 32 KB 的引入数据包被视为32事件，每个事件大小为 1 KB。 允许的最大事件大小为 32 KB。 大于 32 KB 的数据包将被截断。

可以在单一环境中，将 S1 或 S2 SKU 的容量增加到 10 个单位。 不能从 S1 环境迁移到 S2。 不能从 S2 环境迁移到 S1。

对于入口容量，首先要确定每个月所需的入口总数。 接下来，确定每分钟需求。 

限制和延迟在每分钟容量中发挥作用。 如果数据引入的高峰时段持续时间少于24小时，则时序见解可能会以上表中所列速率的两倍的入口速率来 "追赶"。

例如，如果你有一个 S1 SKU，则会按每分钟720事件的速率传输数据，并以1440事件或更低的速率在不到一小时的数据速率高峰，环境中没有明显的延迟。 但是，如果每分钟超过1440个事件的时间超过了1小时，则可能会遇到可视化的数据延迟，并可在环境中进行查询。

您可能事先不知道希望推送的数据量。 在这种情况下，可以在 Azure 门户订阅中查找[Azure IoT 中心](../iot-hub/iot-hub-metrics.md)和[azure 事件中心](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/)的数据遥测。 遥测可以帮助你确定如何预配环境。 使用 "Azure 门户中的"**度量值**"窗格来查看其遥测数据。 了解事件源指标后，可以更有效地计划和预配时序见解环境。

### <a name="calculate-ingress-requirements"></a>计算入口需求

若要计算入口需求：

- 验证输入容量高于每分钟平均速率，并确保您的环境足够大，以便能够在不到一小时的时间内处理与容量相同的预期入口。

- 如果发生的入口峰值超过1小时，则使用峰值速率作为平均值。 为环境预配容量以处理高峰速率。

### <a name="mitigate-throttling-and-latency"></a>缓解限制和延迟

有关如何防止限制和延迟的信息，请参阅[缓解延迟和限制](time-series-insights-environment-mitigate-latency.md)。

## <a name="shape-your-events"></a>塑造事件

务必确保将事件发送到时序见解的方式支持正在设置的环境的大小。 （相反，你可以将环境的大小映射到事件时序见解的数量以及每个事件的大小。）还需要考虑在查询数据时可能想要用来进行切片和筛选的属性。

> [!TIP]
> 查看[发送事件](time-series-insights-send-events.md)中的 JSON 定形文档。

## <a name="ensure-that-you-have-reference-data"></a>确保你有引用数据

*引用数据集*是一组项，用于从事件源中增加事件。 时序见解入口引擎将事件源中的每个事件与引用数据集中的对应数据行联接。 然后，增加的事件可用于查询。 该联接基于在引用数据集中定义的**主键**列。

> [!NOTE]
> 引用数据未联接以追溯方式。 配置并上传后，仅当前和未来的入站数据进行匹配并联接到引用数据集。 如果你计划将大量历史数据发送到时序见解，并且不先上传或创建时序见解中的引用数据，则可能必须重做工作（提示：不太有趣）。  

若要详细了解如何在时序见解中创建、上传和管理引用数据，请阅读我们的[参考数据集文档](time-series-insights-add-reference-data-set.md)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>后续步骤

- 通过[在 Azure 门户中创建新的时序见解环境](time-series-insights-get-started.md)开始。

- 了解如何[将事件中心事件源添加](time-series-insights-how-to-add-an-event-source-eventhub.md)到时序见解。

- 阅读有关如何[配置 IoT 中心事件源](time-series-insights-how-to-add-an-event-source-iothub.md)的信息。
