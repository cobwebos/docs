---
title: "自定义跟踪架构 |Microsoft Docs"
description: "了解有关自定义跟踪架构的详细信息"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: f846f23bef61e8e772920196b9c81d059546d8a6


---
# <a name="custom-tracking-schema"></a>自定义跟踪架构
## <a name="supported-custom-tracking-schema"></a>支持的自定义跟踪架构
````java

        {
            "sourceType": "",
            "source": { 

            "workflow": {
                "systemId": ""
            },
            "runInstance": {
                "runId": ""
            },
            "operation": {
                "operationName": "",
                "repeatItemScopeName": "",
                "repeatItemIndex": "",
                "trackingId": "",
                "correlationId": "",
                "clientRequestId": ""
                }
            },
            "events": [
            {
                "eventLevel": "",
                "eventTime": "",
                "recordType": "",
                "record": {                
                }
            }
         ]
      }

````

| 属性 | 说明 |
| --- | --- |
| sourceType |必需。  它表示运行源的类型。 允许的值为“Microsoft.Logic/workflows”或“自定义” |
| 源 |必需 - 如果源类型为 Microsoft.Logic/workflows，则源信息必须遵循此架构。 如果源类型为“自定义”；架构为 JToken  |
| systemId |必需，字符串。  它表示逻辑应用系统 ID |
| runId |必需，字符串。  它表示逻辑应用运行 ID |
| operationName |必需，字符串。  它表示操作（例如操作或触发器）的名称 |
| repeatItemScopeName |必需，字符串。 如果操作处于 foreach/until 循环内，它表示重复项名称 |
| repeatItemIndex |必需，int。  如果操作处于 foreach/until 循环内，它表示重复项索引  |
| trackingId |可选，字符串。 它表示用于关联消息的跟踪 ID |
| correlationId |可选，字符串。 它表示用于关联消息的相关 ID |
| ClientRequestId |可选，字符串。  客户端可以填充它以关联消息 |
| EventLevel |必需。 它表示事件的级别 |
| EventTime |必需。 它表示事件的时间（UTC 格式 YYYY-MM-DDTHH:MM:SS.00000Z） |
| recordType |必需，它表示跟踪记录的类型。 允许的值为“自定义” |
| record |必需。  它表示自定义记录类型，允许的格式为 JToken |
|  | |

## <a name="supported-b2b-protocol-tracking-schemas"></a>支持的 B2B 协议跟踪架构
* [AS2 跟踪架构](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [X12 跟踪架构](app-service-logic-track-integration-account-x12-tracking-shemas.md) 

## <a name="next-steps"></a>后续步骤
[了解有关监视 B2B 消息的详细信息](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[在 OMS 门户中跟踪 B2B 消息](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[了解有关 Enterprise Integration Pack 的详细信息](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")   




<!--HONumber=Nov16_HO3-->


