---
title: 用于 B2B 消息的 AS2 跟踪架构 - Azure 逻辑应用 | Microsoft Docs
description: 为带有 Enterprise Integration Pack 的 Azure 逻辑应用创建用于监视集成帐户中的 B2B 消息的 AS2 跟踪架构
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 180d90450497b38f107f3601944385a003f50282
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193502"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>为 Azure 逻辑应用创建用于跟踪集成帐户中的 AS2 消息和 MDN 的架构

若要帮助你监视企业到企业 (B2B) 事务的成功、错误和消息属性，可以在集成帐户中使用以下 AS2 跟踪架构：

* AS2 消息跟踪架构
* AS2 MDN 跟踪架构

## <a name="as2-message-tracking-schema"></a>AS2 消息跟踪架构

```json
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
```

| 属性 | Type | 描述 |
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
| isMessageFailed |Boolean | AS2 消息是否失败。 （必需） |
| isMessageSigned | Boolean | AS2 消息是否已签名。 （必需） |
| isMessageEncrypted | Boolean | AS2 消息是否已加密。 （必需） |
| isMessageCompressed |Boolean | AS2 消息是否已压缩。 （必需） |
| correlationMessageId | String | AS2 消息 ID，用于将消息与 MDN 相关联。 (可选) |
| incomingHeaders |JToken 字典 | 传入 AS2 消息标头的详细信息。 (可选) |
| outgoingHeaders |JToken 字典 | 传出 AS2 消息标头的详细信息。 (可选) |
| isNrrEnabled | Boolean | 如果值未知，请使用默认值。 （必需） |
| isMdnExpected | 布尔 | 如果值未知，请使用默认值。 （必需） |
| mdnType | 枚举 | 允许的值为 **NotConfigured**、**Sync** 和 **Async**。 （必需） |
||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN 跟踪架构

```json
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
```

| 属性 | Type | 描述 |
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
| isMessageFailed |Boolean | AS2 消息是否失败。 （必需） |
| isMessageSigned |Boolean | AS2 消息是否已签名。 （必需） |
| isNrrEnabled | Boolean | 如果值未知，请使用默认值。 （必需） |
| statusCode | 枚举 | 允许的值为 **Accepted**、**Rejected** 和 **AcceptedWithErrors**。 （必需） |
| micVerificationStatus | 枚举 | 允许的值为 **NotApplicable**、**Succeeded** 和 **Failed**。 （必需） |
| correlationMessageId | String | 相关性 ID。 原始消息 ID（为其配置 MDN 的消息的消息 ID）。 (可选) |
| incomingHeaders | JToken 字典 | 指示传入消息标头的详细信息。 (可选) |
| outgoingHeaders |JToken 字典 | 指示传出消息标头的详细信息。 (可选) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 协议跟踪架构

有关 B2B 协议跟踪架构的信息，请参阅：

* [X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B 自定义跟踪架构](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

* 了解如何[监视 B2B 消息](logic-apps-monitor-b2b-message.md)
* 了解有关[Azure Monitor 日志中跟踪 B2B 消息](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)