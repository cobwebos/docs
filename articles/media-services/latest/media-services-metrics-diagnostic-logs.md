---
title: 使用 Azure 监视器的媒体服务指标和诊断日志
titleSuffix: Azure Media Services
description: 了解如何通过 Azure 监视器监视 Azure 媒体服务指标和诊断日志。
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
ms.openlocfilehash: 75363212684bb09e84a4bc4881af26e6d2a8e7e3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585273"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>通过 Azure 监视器监视媒体服务指标和诊断日志

[Azure 监视器](../../azure-monitor/overview.md)允许您监视指标和诊断日志，帮助您了解应用的性能。 Azure 监视器收集的所有数据都适合两种基本类型之一：指标和日志。 您可以监视媒体服务诊断日志，并为收集的指标和日志创建警报和通知。 您可以使用[指标资源管理器](../../azure-monitor/platform/metrics-getting-started.md)来可视化和分析指标数据。 您可以将日志发送到[Azure 存储](https://azure.microsoft.com/services/storage/)，将它们流式传输到[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，将其导出到[日志分析](https://azure.microsoft.com/services/log-analytics/)，或使用第三方服务。

有关详细概述，请参阅[Azure 监视器指标](../../azure-monitor/platform/data-platform.md)和[Azure 监视器诊断日志](../../azure-monitor/platform/platform-logs-overview.md)。

本主题讨论受支持的[媒体服务指标](#media-services-metrics)[和媒体服务诊断日志](#media-services-diagnostic-logs)。

## <a name="media-services-metrics"></a>媒体服务指标

不管值是否变化，指标都按固定的时间间隔进行收集。 指标可用于警报，因为它们可以频繁采样，而警报则可以使用相对简单的逻辑快速触发。 有关如何创建指标警报的信息，请参阅使用 Azure[监视器 创建、查看和管理指标警报](../../azure-monitor/platform/alerts-metric.md)。

媒体服务支持以下资源的监视指标：

* Account
* 流式处理终结点

### <a name="account"></a>Account

您可以监视以下帐户指标。

|指标名称|显示名称|描述|
|---|---|---|
|AssetCount|资产计数|您帐户中的资产。|
|AssetQuota|资产配额|您账户中的资产配额。|
|AssetQuotaUsedPercentage|资产配额已用百分比|已使用的资产配额的百分比。|
|ContentKeyPolicyCount|内容密钥策略计数|帐户中的内容关键策略。|
|ContentKeyPolicyQuota|内容密钥策略配额|帐户中的内容关键策略配额。|
|ContentKeyPolicyQuotaUsedPercentage|内容密钥策略配额已用百分比|已使用的内容密钥策略配额的百分比。|
|StreamingPolicyCount|流式处理策略计数|帐户中的流式处理策略。|
|StreamingPolicyQuota|流式处理策略配额|帐户中的流式处理策略配额。|
|StreamingPolicyQuotaUsedPercentage|流式处理策略配额已用百分比|已使用的流式处理策略配额的百分比。|

您还应查看[帐户配额和限制](limits-quotas-constraints.md)。

### <a name="streaming-endpoint"></a>流式处理终结点

支持以下媒体服务[流终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints)指标：

|指标名称|显示名称|描述|
|---|---|---|
|Requests|Requests|提供流式处理终结点提供的 HTTP 请求总数。|
|流出量|流出量|出口字节的总数。 例如，流式处理终结点流式传输的字节。|
|SuccessE2ELatency|成功端到端延迟|从流式处理终结点收到请求到发送响应的最后一个字节的持续时间。|

### <a name="why-would-i-want-to-use-metrics"></a>为什么要使用指标？

以下是监视媒体服务指标如何帮助您了解应用性能的示例。 媒体服务指标可以解决的一些问题包括：

* 如何监视标准流式处理终结点，了解我何时超出了限制？
* 如何知道我是否有足够的高级流式处理终结点缩放单位？
* 如何设置警报，以便知道何时扩展流式处理终结点？
* 如何设置警报，以便知道何时到达了帐户上配置的最大出口？
* 如何查看请求失败的细目以及导致失败的原因？
* 如何查看从打包程序提取的 HLS 或 DASH 请求数？
* 如何设置警报，以便知道故障请求的阈值何时命中？

### <a name="example"></a>示例

请参阅[如何监视媒体服务指标](media-services-metrics-howto.md)。

## <a name="media-services-diagnostic-logs"></a>媒体服务诊断日志

诊断日志提供有关 Azure 资源操作的丰富且频繁的数据。 有关详细信息，请参阅[如何从 Azure 资源收集和使用日志数据](../../azure-monitor/platform/platform-logs-overview.md)。

媒体服务支持以下诊断日志：

* 密钥交付

### <a name="key-delivery"></a>密钥交付

|“属性”|描述|
|---|---|
|密钥交付服务请求|显示密钥传递服务请求信息的日志。 有关详细信息，请参阅[架构](media-services-diagnostic-logs-schema.md)。|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>为什么要使用诊断日志？

您可以使用密钥传递诊断日志检查的一些事项包括：

* 请参阅 DRM 类型提供的许可证数。
* 查看策略交付的许可证数。
* 按 DRM 或策略类型查看错误。
* 查看来自客户端的未授权许可证请求数。

### <a name="example"></a>示例

请参阅[如何监视媒体服务诊断日志](media-services-diagnostic-logs-howto.md)。

## <a name="next-steps"></a>后续步骤

* [如何从 Azure 资源收集和使用日志数据](../../azure-monitor/platform/platform-logs-overview.md)
* [使用 Azure 监视器创建、查看和管理指标警报](../../azure-monitor/platform/alerts-metric.md)
* [如何监视媒体服务指标](media-services-metrics-howto.md)
* [如何监视媒体服务诊断日志](media-services-diagnostic-logs-howto.md)
