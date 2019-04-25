---
title: 监视媒体服务度量值和通过 Azure Monitor 诊断日志 |Microsoft Docs
description: 本文概述了如何监视媒体服务度量值和通过 Azure Monitor 诊断日志。
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
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: 23c87ae92a0f22b4a1a31c054df730af2efc07d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322091"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>监视媒体服务指标和诊断日志

[Azure 监视器](../../azure-monitor/overview.md)允许你监视指标和诊断日志，帮助您理解如何执行你的应用程序。 通过 Azure Monitor 收集的所有数据都适合指标和日志的两种基本类型之一。 您可以监视媒体服务诊断日志，并创建警报和通知收集的指标和日志。 你可以可视化和分析使用指标数据[指标资源管理器](../../azure-monitor/platform/metrics-getting-started.md)。 您可以将日志发送到[Azure 存储](https://azure.microsoft.com/services/storage/)，流式传输到[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，并将其导出到[Log Analytics](https://azure.microsoft.com/services/log-analytics/)，或使用第三方服务。

有关详细的概述，请参阅[Azure Monitor 指标](../../azure-monitor/platform/data-platform.md)并[Azure Monitor 诊断日志](../../azure-monitor/platform/diagnostic-logs-overview.md)。

本主题讨论了当前可用[媒体服务度量值](#media-services-metrics)并[Media Services 诊断日志](#media-services-diagnostic-logs)。

## <a name="media-services-metrics"></a>媒体服务指标

不管值是否变化，指标都按固定的时间间隔进行收集。 指标可用于警报，因为它们可以频繁采样，而警报则可以使用相对简单的逻辑快速触发。

目前，以下媒体服务[流式处理终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints)azure 发出指标：

|Name|描述|
|---|---|
|Requests|提供的服务的流式处理终结点请求总数的详细信息。|
|流出量|总出口字节数。 例如，通过流式处理终结点流式传输的字节数。|
|成功的端到端延迟| 提供有关端到端延迟的成功请求的信息。|

例如，若要使用 CLI"出口"度量值，将运行以下`az monitor metrics`CLI 命令：

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

有关如何创建指标警报的信息，请参阅[创建、 查看和管理使用 Azure Monitor 的指标警报](../../azure-monitor/platform/alerts-metric.md)。

## <a name="media-services-diagnostic-logs"></a>媒体服务诊断日志

目前，可以获取以下诊断日志：

|Name|描述|
|---|---|
|密钥传送服务请求|显示密钥传送服务请求信息的日志。 有关更多详细信息，请参阅[架构](media-services-diagnostic-logs-schema.md)。|

若要启用存储诊断日志的存储帐户中，将运行以下`az monitor diagnostic-settings`CLI 命令： 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

例如：

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>后续步骤 

[如何收集和使用日志数据从 Azure 资源](../../azure-monitor/platform/diagnostic-logs-overview.md)。
