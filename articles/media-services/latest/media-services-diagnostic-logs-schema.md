---
title: Azure 媒体服务诊断日志架构-Azure
description: 本文介绍 Azure 媒体服务诊断日志架构。
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
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 225bc4368d2a87fa5fef3f74624fd10f42dc7c97
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556359"
---
# <a name="diagnostic-logs-schemas"></a>诊断日志架构

[Azure 监视器](../../azure-monitor/overview.md)允许你监视指标和诊断日志，帮助您理解如何执行你的应用程序。 您可以监视媒体服务诊断日志，并创建警报和通知收集的指标和日志。 您可以将日志发送到[Azure 存储](https://azure.microsoft.com/services/storage/)，流式传输到[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，并将其导出到[Log Analytics](https://azure.microsoft.com/services/log-analytics/)，或使用第三方服务。

有关详细信息，请参阅[Azure Monitor 指标](../../azure-monitor/platform/data-collection.md)并[Azure Monitor 诊断日志](../../azure-monitor/platform/diagnostic-logs-overview.md)。

本文介绍媒体服务诊断日志架构。

## <a name="top-level-diagnostic-logs-schema"></a>顶级诊断日志架构

顶级的诊断日志架构的详细说明，请参阅[支持的 Azure 诊断日志服务、 架构和类别](../../azure-monitor/platform/tutorial-dashboards.md)。

## <a name="key-delivery-log-schema"></a>密钥传送日志架构

### <a name="properties"></a>属性

这些属性是特定于密钥传送日志架构。

|名称|描述|
|---|---|
|keyId|请求的项的 ID。|
|keyType|可以是以下值之一："清除"（不加密）、"FairPlay"、"PlayReady"或者"Widevine"。|
|policyName|Azure 资源管理器策略的名称。|
|tokenType|令牌类型。|
|statusMessage|状态消息。|

### <a name="examples"></a>示例

密钥传送请求架构的属性。

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="next-steps"></a>后续步骤

[监视媒体服务指标和诊断日志](media-services-metrics-diagnostic-logs.md)
