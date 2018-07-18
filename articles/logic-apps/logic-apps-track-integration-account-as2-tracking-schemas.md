---
title: 用于 B2B 监视的 AS2 跟踪架构 - Azure 逻辑应用 | Microsoft 文档
description: 使用 AS2 跟踪架构通过 Azure 集成帐户中的事务监视 B2B 消息。
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48e39fd20716e962c4a3e367fdff18e0b4fba32d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300875"
---
# <a name="start-or-enable-tracking-of-as2-messages-and-mdns-to-monitor-success-errors-and-message-properties"></a>启动或启用对 AS2 消息和 MDN 的跟踪，监视成功、错误和消息属性
可以在 Azure 集成帐户中使用这些 AS2 跟踪架构来帮助监视企业到企业 (B2B) 的事务：

* AS2 消息跟踪架构
* AS2 MDN 跟踪架构

## <a name="as2-message-tracking-schema"></a>AS2 消息跟踪架构
````java

    {
       "agreementProperties": {  
            "senderPartnerName": "",  
            "receiverPartnerName": "",  
            "as2To": "",  
            "as2From": "",  
            "agreementName": ""  
        },  
        "messageProperties": {
            "direction": "",
            "messageId": "",
            "dispositionType": "",
            "fileName": "",
            "isMessageFailed": "",
            "isMessageSigned": "",
            "isMessageEncrypted": "",
            "isMessageCompressed": "",
            "correlationMessageId": "",
            "incomingHeaders": {
            },
            "outgoingHeaders": {
            },
        "isNrrEnabled": "",
        "isMdnExpected": "",
        "mdnType": ""
        }
    }
````

| 属性 | Type | 说明 |
| --- | --- | --- |
| senderPartnerName | String | AS2 消息发送者的合作伙伴名称。 (可选) |
| receiverPartnerName | String | AS2 消息接收者的合作伙伴名称。 (可选) |
| as2To | String | AS2 消息接收者的名称，来自 AS2 消息的标头。 （必需） |
| as2From | String | AS2 消息发送者的名称，来自 AS2 消息的标头。 （必需） |
| agreementName | String | 解析消息的 AS2 协议名称。 (可选) |
| direction | String | 消息流的方向，接收或发送。 （必需） |
| messageId | String | AS2 消息 ID，来自 AS2 消息的标头（可选） |
| dispositionType |String | 邮件处置说明 (MDN) 处置类型值。 (可选) |
| fileName | String | 文件名，来自 AS2 消息的标头。 (可选) |
| isMessageFailed |布尔 | AS2 消息是否失败。 （必需） |
| isMessageSigned | 布尔 | AS2 消息是否已签名。 （必需） |
| isMessageEncrypted | 布尔 | AS2 消息是否已加密。 （必需） |
| isMessageCompressed |布尔 | AS2 消息是否已压缩。 （必需） |
| correlationMessageId | String | AS2 消息 ID，用于将消息与 MDN 相关联。 (可选) |
| incomingHeaders |JToken 字典 | 传入 AS2 消息标头的详细信息。 (可选) |
| outgoingHeaders |JToken 字典 | 传出 AS2 消息标头的详细信息。 (可选) |
| isNrrEnabled | 布尔 | 如果值未知，请使用默认值。 （必需） |
| isMdnExpected | 布尔 | 如果值未知，请使用默认值。 （必需） |
| mdnType | 枚举 | 允许的值为 **NotConfigured**、**Sync** 和 **Async**。 （必需） |

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN 跟踪架构
````java

    {
        "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "as2To": "",
                "as2From": "",
                "agreementName": "g"
            },
            "messageProperties": {
                "direction": "",
                "messageId": "",
                "originalMessageId": "",
                "dispositionType": "",
                "isMessageFailed": "",
                "isMessageSigned": "",
                "isNrrEnabled": "",
                "statusCode": "",
                "micVerificationStatus": "",
                "correlationMessageId": "",
                "incomingHeaders": {
                },
                "outgoingHeaders": {
                }
            }
    }
````

| 属性 | Type | 说明 |
| --- | --- | --- |
| senderPartnerName | String | AS2 消息发送者的合作伙伴名称。 (可选) |
| receiverPartnerName | String | AS2 消息接收者的合作伙伴名称。 (可选) |
| as2To | String | 接收 AS2 消息的合作伙伴名称。 （必需） |
| as2From | String | 发送 AS2 消息的合作伙伴名称。 （必需） |
| agreementName | String | 解析消息的 AS2 协议名称。 (可选) |
| direction |String | 消息流的方向，接收或发送。 （必需） |
| messageId | String | AS2 消息 ID。 (可选) |
| originalMessageId |String | AS2 原始消息 ID。 (可选) |
| dispositionType | String | MDN 处置类型值。 (可选) |
| isMessageFailed |布尔 | AS2 消息是否失败。 （必需） |
| isMessageSigned |布尔 | AS2 消息是否已签名。 （必需） |
| isNrrEnabled | 布尔 | 如果值未知，请使用默认值。 （必需） |
| statusCode | 枚举 | 允许的值为 **Accepted**、**Rejected** 和 **AcceptedWithErrors**。 （必需） |
| micVerificationStatus | 枚举 | 允许的值为 **NotApplicable**、**Succeeded** 和 **Failed**。 （必需） |
| correlationMessageId | String | 相关性 ID。 原始消息 ID（为其配置 MDN 的消息的消息 ID）。 (可选) |
| incomingHeaders | JToken 字典 | 指示传入消息标头的详细信息。 (可选) |
| outgoingHeaders |JToken 字典 | 指示传出消息标头的详细信息。 (可选) |

## <a name="next-steps"></a>后续步骤
* 了解有关 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) 的详细信息。    
* 了解有关[监视 B2B 消息](logic-apps-monitor-b2b-message.md)的详细信息。   
* 了解有关 [B2B 自定义跟踪架构](logic-apps-track-integration-account-custom-tracking-schema.md)的详细信息。   
* 了解有关 [X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md)的详细信息。   
* 了解[在 Log Analytics 中跟踪 B2B 消息](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。
