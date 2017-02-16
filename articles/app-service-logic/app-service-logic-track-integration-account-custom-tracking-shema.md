---
title: "自定义跟踪架构 | Microsoft Docs"
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
ms.sourcegitcommit: d88fa57c2f343636d7529780dc1b27ccb604ee02
ms.openlocfilehash: 0de8128b01f760340e85078b433707c566fa2e4f


---
# <a name="custom-tracking-schemas"></a>自定义跟踪架构
可以在 Azure 集成帐户中使用自定义跟踪架构来帮助监视企业到企业 (B2B) 事务。

## <a name="custom-tracking-schema"></a>自定义跟踪架构
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

| 属性 | 类型 | 说明 |
| --- | --- | --- |
| sourceType |   | 运行的源的类型。 允许的值为“Microsoft.Logic/workflows”和“自定义”。 （必需） |
| 源 |   | 如果源类型为 **Microsoft.Logic/workflows**，则源信息必须遵循此架构。 如果源类型为“自定义”；架构为 JToken。 （必需） |
| systemId | String | 逻辑应用系统 ID。 （必需） |
| runId | String | 逻辑应用运行 ID。 （必需） |
| operationName | String | 操作（例如操作或触发）的名称。 （必需） |
| repeatItemScopeName | String | 如果操作位于 `foreach`/`until` 循环内，请重复项名称。 （必需） |
| repeatItemIndex | Integer | 操作是否位于 `foreach`/`until` 循环内。 指示重复的项索引。 （必需） |
| trackingId | String | 跟踪 ID，用于关联消息。 (可选) |
| correlationId | String | 相关性 ID，用于关联消息。 (可选) |
| ClientRequestId | String | 客户端可以填充它以关联消息。 (可选) |
| EventLevel |   | 事件的级别。 （必需） |
| EventTime |   | 事件的时间（UTC 格式 YYYY-MM-DDTHH:MM:SS.00000Z）。 （必需） |
| recordType |   | 跟踪记录的类型。 允许的值为“自定义”。 （必需） |
| record |   | 自定义记录类型。 允许的格式为 JToken。 （必需） |

## <a name="b2b-protocol-tracking-schemas"></a>B2B 协议跟踪架构
有关 B2B 协议跟踪架构的信息，请参阅：
* [AS2 跟踪架构](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [X12 跟踪架构](app-service-logic-track-integration-account-x12-tracking-shemas.md)

## <a name="next-steps"></a>后续步骤
* [了解有关监视 B2B 消息的详细信息](app-service-logic-monitor-b2b-message.md)。   
* 了解[在 Operations Management Suite 门户中跟踪 B2B 消息](app-service-logic-track-b2b-messages-omsportal.md)。
* [了解有关 Enterprise Integration Pack 的详细信息](app-service-logic-enterprise-integration-overview.md)。



<!--HONumber=Dec16_HO3-->


