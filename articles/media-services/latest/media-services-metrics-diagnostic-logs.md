---
title: 使用 Azure Monitor 监视媒体服务指标和诊断日志
titleSuffix: Azure Media Services
description: 了解如何通过 Azure Monitor 监视 Azure 媒体服务指标和诊断日志。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c03950d64c9ead17dfa5c07ef70ab2b7ee0e90bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296645"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>通过 Azure Monitor 监视媒体服务指标和诊断日志

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

使用 [Azure Monitor](../../azure-monitor/overview.md) 可以监视指标和诊断日志，以帮助了解应用的执行情况。 Azure Monitor 收集的所有数据属于以下两种基本类型之一：指标和日志。 可以监视媒体服务诊断日志，并针对收集的指标和日志创建警报与通知。 可以使用[指标资源管理器](../../azure-monitor/platform/metrics-getting-started.md)来可视化和分析指标数据。 可将日志发送到 [Azure 存储](https://azure.microsoft.com/services/storage/)、将其流式传输到 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)、并将其导出到 [Log Analytics](https://azure.microsoft.com/services/log-analytics/)，或使用第三方服务。

有关详细概述，请参阅 [Azure Monitor 指标](../../azure-monitor/platform/data-platform.md)和 [Azure Monitor 诊断日志](../../azure-monitor/platform/platform-logs-overview.md)。

本主题介绍支持的[媒体服务指标](#media-services-metrics)和[媒体服务诊断日志](#media-services-diagnostic-logs)。

## <a name="media-services-metrics"></a>媒体服务指标

不管值是否变化，指标都按固定的时间间隔进行收集。 指标可用于警报，因为它们可以频繁采样，而警报则可以使用相对简单的逻辑快速触发。 有关如何创建指标警报的信息，请参阅[使用 Azure Monitor 创建、查看和管理指标警报](../../azure-monitor/platform/alerts-metric.md)。

媒体服务支持监视以下资源的指标：

* 帐户
* 流式处理终结点

### <a name="account"></a>帐户

可监视以下帐户指标。

|指标名称|Display name|说明|
|---|---|---|
|AssetCount|资产计数|帐户中的资产。|
|AssetQuota|资产配额|帐户中的资产配额。|
|AssetQuotaUsedPercentage|资产配额已用百分比|已使用的资产配额百分比。|
|ContentKeyPolicyCount|内容密钥策略计数|帐户中的内容密钥策略。|
|ContentKeyPolicyQuota|内容密钥策略配额|帐户中的内容密钥策略配额。|
|ContentKeyPolicyQuotaUsedPercentage|内容密钥策略配额已用百分比|已使用的内容密钥策略配额百分比。|
|StreamingPolicyCount|流式处理策略计数|帐户中的流式处理策略。|
|StreamingPolicyQuota|流式处理策略配额|帐户中的流式处理策略配额。|
|StreamingPolicyQuotaUsedPercentage|流式处理策略配额已用百分比|已使用的流式处理策略配额百分比。|

还应查看[帐户配额和限制](limits-quotas-constraints.md)。

### <a name="streaming-endpoint"></a>流式处理终结点

支持以下媒体服务[流式处理终结点](/rest/api/media/streamingendpoints)指标：

|指标名称|Display name|说明|
|---|---|---|
|请求|请求|提供由流式处理终结点提供服务的 HTTP 请求总数。|
|流出量|流出量|每个流式处理终结点每分钟流出的字节总数。|
|SuccessE2ELatency|成功端到端延迟|从流式处理终结点收到请求，到发送最后一个响应字节的持续时间。|

### <a name="why-would-i-want-to-use-metrics"></a>为何使用指标？

以下示例说明监视媒体服务指标如何能够帮助你了解应用的执行情况。 使用媒体服务指标可以解决的部分问题包括：

* 如何监视标准流式处理终结点以了解何时超出了限制？
* 如何知道是否有足够的高级流式处理终结点缩放单元？
* 如何设置警报来了解何时应扩展流式处理终结点？
* 如何设置警报来了解何时达到了帐户中配置的最大出口限制？
* 如何查看失败请求的细节以及失败的原因？
* 如何查看正在从打包程序中提取多少个 HLS 或 DASH 请求？
* 如何设置警报来了解何时达到了失败请求数的阈值？

### <a name="example"></a>示例

参阅[如何监视媒体服务指标](media-services-metrics-howto.md)。

## <a name="media-services-diagnostic-logs"></a>媒体服务诊断日志

诊断日志提供有关 Azure 资源操作的丰富、频繁的数据。 有关详细信息，请参阅[如何从 Azure 资源收集和使用日志数据](../../azure-monitor/platform/platform-logs-overview.md)。

媒体服务支持以下诊断日志：

* 密钥传送

### <a name="key-delivery"></a>密钥传送

|名称|说明|
|---|---|
|密钥传送服务请求|显示密钥传送服务请求信息的日志。 有关详细信息，请参阅[架构](media-services-diagnostic-logs-schema.md)。|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>为何使用诊断日志？

可以使用密钥传送诊断日志检查的部分内容包括：

* 按 DRM 类型查看传送的许可证数量。
* 按策略查看传送的许可证数量。
* 按 DRM 或策略类型查看错误。
* 查看来自客户端的未经授权的许可证请求数。

### <a name="example"></a>示例

参阅[如何监视媒体服务诊断日志](media-services-diagnostic-logs-howto.md)。

## <a name="next-steps"></a>后续步骤

* [如何从 Azure 资源收集和使用日志数据](../../azure-monitor/platform/platform-logs-overview.md)
* [使用 Azure Monitor 创建、查看和管理指标警报](../../azure-monitor/platform/alerts-metric.md)
* [如何监视媒体服务指标](media-services-metrics-howto.md)
* [如何监视媒体服务诊断日志](media-services-diagnostic-logs-howto.md)
