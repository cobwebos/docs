---
title: 规划 Azure 时序见解预览版环境 | Microsoft Docs
description: 计划 Azure 时序见解预览版环境。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 09fef060a58723624796fe6192fef2444251cc1c
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556078"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>计划 Azure 时序见解预览版环境

本指南介绍有关快速计划和开始使用 Azure 时序见解预览版的最佳做法。

## <a name="best-practices-for-planning-and-preparation"></a>有关计划和准备的最佳实践

若要开始使用时序见解，最好是了解：

* 在预配时序见解预览版环境时获得的功能。
* 时序 ID 和时间戳属性是什么。
* 新时序模型是什么，以及如何生成自己的模型。
* 如何在 JSON 中高效地发送事件。 
* 时序见解业务灾难恢复选项。

时序见解采用即用即付业务模型。 有关费用和容量的详细信息，请参阅[时序见解定价](https://azure.microsoft.com/pricing/details/time-series-insights/)。

## <a name="the-time-series-insights-preview-environment"></a>时序见解预览版环境

预配 Azure 时序见解预览版环境时，会创建两个 Azure 资源：

* 时序见解预览版环境
* Azure 存储常规用途 V1 帐户

若要开始，需要三个附加项：
 
- [时序模型](./time-series-insights-update-tsm.md) 
- [连接到时序见解的事件源](./time-series-insights-how-to-add-an-event-source-iothub.md) 
- [事件源的事件](./time-series-insights-send-events.md)它们映射到模型并且采用有效 JSON 格式 

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>配置时序 ID 和时间戳属性

若要创建新的时序见解环境，请选择时序 ID。 此操作用作数据的逻辑分区。 如前所述，请确保时序 ID 已准备就绪。

> [!IMPORTANT]
> 时序 ID 不可变并且以后无法更改。 在进行最终选择和首次使用之前验证每个 ID。

可以选择最多三 (3) 个键以唯一地区分资源。 有关详细信息，请阅读[选择时序 ID 的最佳做法](./time-series-insights-update-how-to-id.md)和[存储和入口](./time-series-insights-update-storage-ingress.md)。

时间戳属性也十分重要。 可以在添加事件源时指定此属性。 每个事件源都有一个可选的时间戳属性，它用于随时间推移跟踪事件源。 时间戳值区分大小写，并且必须按照每个事件源的单独规范设置格式。

> [!TIP]
> 验证事件源的格式设置和分析要求。

如果留空，则事件源的事件排队时间会用作事件时间戳。 如果发送历史数据或批处理事件，则自定义时间戳属性比默认事件排队时间更有帮助。 有关详细信息，请阅读[如何在 IoT 中心中添加事件源](./time-series-insights-how-to-add-an-event-source-iothub.md)。 

## <a name="understand-the-time-series-model"></a>了解时序模型

现在可以配置时序见解环境的时序模型。 通过新模型可以轻松查找和分析 IoT 数据。 它可实现时序数据的特选、维护和扩充，并可帮助准备供使用者使用的数据集。 模型使用**时间系列 ID**，它们映射到将唯一资源与变量（称为类型）和层次结构关联的实例。 了解新的[时序模型](./time-series-insights-update-tsm.md)。

模型是动态的，因此可以随时生成。 若要快速开始，请先生成并上传它，然后再将数据推送到时序见解。 若要生成模型，请参阅[使用时序模型](./time-series-insights-update-how-to-tsm.md)。

对于许多客户而言，时序模型映射到已实施的现有资产模型或 ERP 系统。 如果没有现有模型，则[提供](https://github.com/Microsoft/tsiclient)了预生成用户体验以快速启动并运行。 若要了解模型可如何帮助你，请查看[示例演示环境](https://insights.timeseries.azure.com/preview/demo)。 

## <a name="shape-your-events"></a>塑造事件

可以验证将事件发送到时序见解的方法。 理想情况下，事件非规范化且高效。

一个好的经验法则是：

* 将元数据存储在时序模型中
* 时序模式、实例字段和事件仅包括必要信息，例如：
  * 时序 ID
  * Timestamp

有关详细信息，请参[塑造事件](./time-series-insights-send-events.md#json)。

## <a name="business-disaster-recovery"></a>业务灾难恢复

时序见解是在 Azure 区域级别使用冗余的高可用性服务。 无需配置即可使用这些固有的功能。 此外，Microsoft Azure 平台还包含了相关功能来帮助你构建提供灾难恢复功能或跨区域可用性的解决方案。 若要为设备或用户提供全局性的跨区域高可用性，请利用这些 Azure 灾难恢复功能。 

有关 Azure 中用于业务连续性和灾难恢复 (BCDR) 的内置功能的信息，请参阅 [Azure 业务连续性技术指南](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance)。 有关 Azure 应用程序的高可用性和灾难恢复实现策略的体系结构指南，请参阅 [Azure 应用程序的灾难恢复和高可用性](https://docs.microsoft.com/azure/architecture/resiliency/index)一文。

> [!NOTE]

>  时序见解没有内置 BCDR。
> 默认情况下，Azure 存储、Azure IoT 中心和 Azure 事件中心具有内置恢复功能。

若要了解详细信息，请阅读：

* [Azure 存储冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [IoT 中心高可用性灾难恢复](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)
* [事件中心策略](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)

如果需要 BCDR，则仍然可以实现恢复策略。 在备份 Azure 区域中创建第二个时序见解环境。 从主要事件源将事件发送到此辅助环境。 使用另一个专用使用者组和该事件源的 BCDR 指南。

按照以下步骤来创建和使用辅助时序见解环境。

1. 在另一个区域中创建环境。 有关详细信息，请参阅[时序见解环境](./time-series-insights-get-started.md)。
1. 为事件源创建另一个专用使用者组。 将该事件源连接到新环境。 请务必指定第二个专用使用者组。 若要了解详细信息，请参阅 [IoT 中心文档](./time-series-insights-how-to-add-an-event-source-iothub.md)或[事件中心文档](./time-series-insights-data-access.md)。
1. 如果主要区域在灾难事件期间受到影响，请将操作重新路由到备用时序见解环境。

> [!IMPORTANT]
> * 请注意，可能会进行故障转移时遇到延迟。
> * 故障转移还可能会导致消息处理中出现短暂峰值，因为在重新路由操作。
> * 有关详细信息，请参阅[缓解时序见解中的延迟](./time-series-insights-environment-mitigate-latency.md)。

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请阅读：

- [Azure 时序见解预览版存储和入口](./time-series-insights-update-storage-ingress.md)
- [数据建模](./time-series-insights-update-tsm.md)