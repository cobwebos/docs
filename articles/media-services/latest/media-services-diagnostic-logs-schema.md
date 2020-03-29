---
title: Azure 媒体服务诊断日志架构 - Azure
description: 本文演示 Azure 媒体服务诊断日志架构。
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
ms.openlocfilehash: 37baed076ca074c1d558af36649e90959a0034c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750873"
---
# <a name="diagnostic-logs-schemas"></a>诊断日志架构

[Azure 监视器](../../azure-monitor/overview.md)使您能够监视指标和诊断日志，以帮助您了解应用程序的性能。 您可以监视媒体服务诊断日志，并为收集的指标和日志创建警报和通知。 您可以将日志发送到[Azure 存储](https://azure.microsoft.com/services/storage/)，将它们流式传输到[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，并将它们导出到[日志分析](https://azure.microsoft.com/services/log-analytics/)，或使用第三方服务。

有关详细信息，请参阅[Azure 监视器指标](../../azure-monitor/platform/data-platform.md)和[Azure 监视器诊断日志](../../azure-monitor/platform/platform-logs-overview.md)。

本文介绍了媒体服务诊断日志架构。

## <a name="top-level-diagnostic-logs-schema"></a>顶级诊断日志架构

有关顶级诊断日志架构的详细描述，请参阅[Azure 诊断日志的支持服务、架构和类别](../../azure-monitor/platform/tutorial-dashboards.md)。

## <a name="key-delivery-log-schema"></a>密钥交付日志架构

### <a name="properties"></a>属性

这些属性特定于密钥传递日志架构。

|“属性”|描述|
|---|---|
|keyId|请求的密钥的 ID。|
|keyType|可以是以下值之一："清除"（无加密）、"公平播放"、"播放就绪"或"威文"。|
|policyName|策略的 Azure 资源管理器名称。|
|tokenType|标记类型。|
|状态消息|状态消息。|

### <a name="examples"></a>示例

密钥传递请求架构的属性。

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

## <a name="additional-notes"></a>附加说明

* Widevine 是 Google Inc. 提供的一项服务，并受 Google Inc. 服务条款和隐私策略的约束。

## <a name="next-steps"></a>后续步骤

[监视媒体服务指标和诊断日志](media-services-metrics-diagnostic-logs.md)
