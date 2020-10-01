---
title: 规划第 2 代环境 - Azure 时序见解 | Microsoft Docs
description: 配置、管理、规划和部署 Azure 时序见解第 2 代环境的最佳做法。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: cb12777a6a4fa1e75cd65bc597c87442d592aad5
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91598116"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>规划 Azure 时序见解 Gen2 环境

本文介绍有关快速规划和开始使用 Azure 时序见解第 2 代的最佳做法。

## <a name="best-practices-for-planning-and-preparation"></a>有关计划和准备的最佳实践

围绕环境规划和准备的最佳做法在以下文章中进一步进行了阐述：

* [预配 Azure 时序见解 Gen2 环境](#the-gen2-environment)时获得的内容。
* [时序 ID 和时间戳属性](#configure-time-series-ids-and-timestamp-properties)是什么。
* [新时序模型](#understand-the-time-series-model)是什么，以及如何生成自己的模型。
* 如何[在 JSON 中高效地发送事件](#shape-your-events)。
* Azure 时序见解[业务灾难恢复选项](#business-disaster-recovery)。

Azure 时序见解采用即用即付业务模型。 若要详细了解费用和容量，请阅读 [Azure 时序见解定价](https://azure.microsoft.com/pricing/details/time-series-insights/)。

## <a name="the-gen2-environment"></a>第 2 代环境

预配 Azure 时序见解 Gen2 环境时，会创建两个 Azure 资源：

* Azure 时序见解第 2 代环境
* Azure 存储帐户

我们在预配过程中指定是否要启用暖存储。 暖存储提供分层的查询体验。 启用后，必须指定 7 到 30 天的保留期。 在暖存储保留期内执行的查询通常响应时间更短。 如果查询跨越暖存储保留期，系统会从冷存储为其提供服务。

在暖存储上进行的查询免费，而在冷存储上进行的查询则收费。 必须了解查询模式并对暖存储配置进行相应的计划。 建议将对最新数据进行的交互式分析驻留在暖存储中，将模式分析和长期趋势驻留在冷存储中。

> [!NOTE]
> 若要详细了解如何查询暖数据，请阅读 [API 参考](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters)。

若要开始，需要三个附加项：

* [时序模型](./concepts-model-overview.md)
* [连接到时序见解的事件源](./concepts-streaming-ingestion-event-sources.md)
* [事件源的事件](./time-series-insights-send-events.md)它们映射到模型并且采用有效 JSON 格式

## <a name="review-azure-time-series-insights-gen2-limits"></a>查看 Azure 时序见解第 2 代限制

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>配置时序 ID 和时间戳属性

若要创建新的 Azure 时序见解环境，请选择时序 ID。 此操作用作数据的逻辑分区。 如前所述，请确保时序 ID 已准备就绪。

> [!IMPORTANT]
> 时序 ID 以后不能更改。 在进行最终选择和首次使用之前验证每个 ID。

可以选择最多三个键以唯一区分资源。 有关详细信息，请阅读[选择时序 ID 的最佳做法](./time-series-insights-update-how-to-id.md)和[引入规则](concepts-json-flattening-escaping-rules.md)。

时间戳属性也十分重要。 可以在添加事件源时指定此属性。 每个事件源都有一个可选的时间戳属性，它用于随时间推移跟踪事件源。 时间戳值区分大小写，并且必须按照每个事件源的单独规范设置格式。

留空时，事件排队进入 IoT 中心或事件中心的时间将用作事件时间戳。 通常，用户应选择自定义时间戳属性，并使用传感器或标记生成读取的时间，而不是中心排队时间。 有关详细信息，以及如何读取时区偏移量读取 [事件源时间戳](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp)。

## <a name="understand-the-time-series-model"></a>了解时序模型

现在可以配置 Azure 时序见解环境的时序模型。 通过新模型可以轻松查找和分析 IoT 数据。 它可实现时序数据的特选、维护和扩充，并可帮助准备供使用者使用的数据集。 模型使用时序 ID，这些 ID 映射到将唯一资源与变量（称为类型）和层次结构关联的实例。 请阅读[时序模型](./concepts-model-overview.md)概述以了解详情。

模型是动态的，因此可以随时生成。 若要快速开始，请先生成并上传它，然后再将数据推送到 Azure 时序见解。 若要生成模型，请阅读[使用时序模型](/azure/time-series-insights/concepts-model-overview)。

对于许多客户而言，时序模型映射到已实施的现有资产模型或 ERP 系统。 如果没有现有模型，则[提供](https://github.com/Microsoft/tsiclient)了预生成用户体验以快速启动并运行。 若要了解模型可如何帮助你，请查看[示例演示环境](https://insights.timeseries.azure.com/preview/demo)。

## <a name="shape-your-events"></a>塑造事件

可以验证将事件发送到 Azure 时序见解的方法。 理想情况下，事件非规范化且高效。

一个好的经验法则是：

* 将元数据存储在时序模型中。
* 确保时序模式、实例字段和事件仅包括必要信息，例如：时序 ID 或时间戳属性。

若要详细了解如何平展和存储事件，请阅读 [JSON 平展和转义规则](./concepts-json-flattening-escaping-rules.md)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>后续步骤

* 若要规划业务恢复配置选项，请查看 [Azure 顾问](../advisor/advisor-overview.md)。
* 若要规划业务恢复配置选项，请查看 [Azure 顾问](../advisor/advisor-overview.md)。
* 阅读有关 Azure 时序见解第 2 代中[数据引入](./concepts-ingestion-overview.md)的详细信息。
* 查看有关 Azure 时序见解第 2 代中[数据存储](./concepts-storage.md)的文章。
* 了解 Azure 时序见解第 2 代中的[数据建模](./concepts-model-overview.md)。
