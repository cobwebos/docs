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
ms.openlocfilehash: 5e416087df6c27eff42db487e90ff908d7d49df8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977686"
---
# <a name="diagnostic-logs-schemas"></a>诊断日志架构

[Azure Monitor](../../azure-monitor/overview.md)使你能够监视指标和诊断日志，以帮助你了解应用程序的执行情况。 可以监视媒体服务诊断日志，并为收集的指标和日志创建警报和通知。 可以将日志发送到[Azure 存储](https://azure.microsoft.com/services/storage/)，将日志流式传输到[azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，然后将其导出到[Log Analytics](https://azure.microsoft.com/services/log-analytics/)或使用第三方服务。

有关详细信息，请参阅[Azure Monitor 度量值](../../azure-monitor/platform/data-platform.md)和[Azure Monitor 诊断日志](../../azure-monitor/platform/resource-logs-overview.md)。

本文介绍 Media Services 诊断日志架构。

## <a name="top-level-diagnostic-logs-schema"></a>顶级诊断日志架构

有关顶层诊断日志架构的详细说明，请参阅[Azure 诊断日志支持的服务、架构和类别](../../azure-monitor/platform/tutorial-dashboards.md)。

## <a name="key-delivery-log-schema"></a>密钥传送日志架构

### <a name="properties"></a>属性

这些属性特定于密钥传送日志架构。

|名称|描述|
|---|---|
|keyId|请求的密钥的 ID。|
|keyType|可以是以下值之一： "Clear" （无加密）、"FairPlay"、"PlayReady" 或 "Widevine"。|
|policyName|策略的 Azure 资源管理器名称。|
|tokenType|令牌类型。|
|statusMessage|状态消息。|

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

* Widevine 是 Google Inc. 提供的一项服务，受 Google，Inc. 的服务条款和隐私策略的约束。

## <a name="next-steps"></a>后续步骤

[监视媒体服务指标和诊断日志](media-services-metrics-diagnostic-logs.md)
