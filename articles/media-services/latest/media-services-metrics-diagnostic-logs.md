---
title: 监视 Azure 媒体服务度量值和通过 Azure Monitor 诊断日志 |Microsoft Docs
description: 本文概述了如何监视 Azure 媒体服务度量值和通过 Azure Monitor 诊断日志。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 6d26cd809d78bf05f66c9fa03be5063ca4d2d5e4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806001"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>监视媒体服务指标和诊断日志

[Azure 监视器](../../azure-monitor/overview.md)允许你监视指标和诊断日志，帮助您理解如何执行你的应用程序。 Azure Monitor 收集的所有数据属于以下两种基本类型之一：指标和日志。 您可以监视媒体服务诊断日志，并创建警报和通知收集的指标和日志。 你可以可视化和分析使用指标数据[指标资源管理器](../../azure-monitor/platform/metrics-getting-started.md)。 您可以将日志发送到[Azure 存储](https://azure.microsoft.com/services/storage/)，流式传输到[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，并将其导出到[Log Analytics](https://azure.microsoft.com/services/log-analytics/)，或使用第三方服务。

有关详细的概述，请参阅[Azure Monitor 指标](../../azure-monitor/platform/data-platform.md)并[Azure Monitor 诊断日志](../../azure-monitor/platform/diagnostic-logs-overview.md)。

本主题讨论支持[媒体服务度量值](#media-services-metrics)并[Media Services 诊断日志](#media-services-diagnostic-logs)。

## <a name="media-services-metrics"></a>媒体服务指标

不管值是否变化，指标都按固定的时间间隔进行收集。 指标可用于警报，因为它们可以频繁采样，而警报则可以使用相对简单的逻辑快速触发。 有关如何创建指标警报的信息，请参阅[创建、 查看和管理使用 Azure Monitor 的指标警报](../../azure-monitor/platform/alerts-metric.md)。

媒体服务支持以下资源监控指标：

* 帐户
* 流式处理终结点
 
### <a name="account"></a>帐户

你可以监视以下帐户指标。 

|指标名称|Display name|描述|
|---|---|---|
|AssetCount|资产计数|你的帐户中的资产。|
|AssetQuota|资产配额|在你的帐户中的资产配额。|
|AssetQuotaUsedPercentage|资产已使用的配额百分比|已使用的资产配额的百分比。|
|ContentKeyPolicyCount|内容密钥策略计数|在你的帐户中的内容密钥策略。|
|ContentKeyPolicyQuota|内容密钥策略配额|在你的帐户中的内容密钥策略配额。|
|ContentKeyPolicyQuotaUsedPercentage|内容密钥策略配额使用百分比|已使用的内容密钥策略配额的百分比。|
|StreamingPolicyCount|流式处理的策略数|在你的帐户中的流式处理策略。|
|StreamingPolicyQuota|流式处理策略配额|在你的帐户中的流式处理策略配额。|
|StreamingPolicyQuotaUsedPercentage|流式处理策略配额使用百分比|已使用的流式处理策略配额的百分比。|
 
此外应查看[帐户的配额和限制](limits-quotas-constraints.md)。

### <a name="streaming-endpoint"></a>流式处理终结点

以下 Media Services[流式处理终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints)支持指标：

|指标名称|Display name|描述|
|---|---|---|
|Requests|Requests|提供了服务的流式处理终结点的 HTTP 请求的总数。|
|流出量|流出量|总出口字节数。 例如，通过流式处理终结点流式传输的字节数。|
|SuccessE2ELatency|成功的端到端延迟|持续时间从流式处理终结点时接收到响应的最后一个字节何时已发送请求。|

### <a name="why-would-i-want-to-use-metrics"></a>我为什么要使用度量值？ 

以下是示例说明如何监视媒体服务度量值可以帮助你了解如何执行你的应用程序。 可以使用媒体服务度量值进行寻址的一些问题是：

* 如何监视我标准流式处理终结点时我已超过限制？
* 如何知道是否我有足够高级流式处理终结点的缩放单位？ 
* 如何设置警报以了解何时纵向扩展我的流式处理终结点？
* 如何设置警报，以便知道已达到最大出口的帐户上配置？
* 如何查看失败的请求的明细，并导致失败的原因？
* 如何查看正在从包装程序中提取多少个 HLS 或 DASH 请求？
* 如何设置警报，以便了解何时命中失败的请求数阈值？ 

### <a name="example"></a>示例

请参阅[如何监视媒体服务指标](media-services-metrics-howto.md)

## <a name="media-services-diagnostic-logs"></a>媒体服务诊断日志

诊断日志提供有关 Azure 资源操作频繁生成的丰富数据。 有关详细信息，请参阅[如何收集和使用日志数据从 Azure 资源](../../azure-monitor/platform/diagnostic-logs-overview.md)。

媒体服务支持以下诊断日志：

* 密钥传送

### <a name="key-delivery"></a>密钥传送

|名称|描述|
|---|---|
|密钥传送服务请求|显示密钥传送服务请求信息的日志。 有关更多详细信息，请参阅[架构](media-services-diagnostic-logs-schema.md)。|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>我为什么要使用诊断日志？ 

你可以检查密钥传送诊断日志的一些事项为：

* 请参阅 DRM 类型由提供的许可证数量
* 请参阅由策略提供的许可证数量 
* 请参阅 DRM 或策略类型的错误
* 请参阅从客户端的未经授权的许可证请求数

### <a name="example"></a>示例

请参阅[如何监视媒体服务诊断日志](media-services-diagnostic-logs-howto.md)

## <a name="next-steps"></a>后续步骤 

* [如何收集和使用日志数据从 Azure 资源](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [创建、 查看和管理使用 Azure Monitor 的指标警报](../../azure-monitor/platform/alerts-metric.md)
* [如何监视媒体服务指标](media-services-metrics-howto.md)
* [如何监视媒体服务诊断日志](media-services-diagnostic-logs-howto.md)
