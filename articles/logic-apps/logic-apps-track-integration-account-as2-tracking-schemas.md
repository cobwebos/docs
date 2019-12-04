---
title: B2B 消息的 AS2 跟踪架构
description: 为带有 Enterprise Integration Pack 的 Azure 逻辑应用创建用于监视集成帐户中的 B2B 消息的 AS2 跟踪架构
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 515d7cfc985ee9929f70de2c862170ff79ae4d60
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792810"
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

| properties | Type | 描述 |
| --- | --- | --- |
| senderPartnerName | 字符串 | AS2 消息发送者的合作伙伴名称。 (可选) |
| receiverPartnerName | 字符串 | AS2 消息接收者的合作伙伴名称。 (可选) |
| as2To | 字符串 | AS2 消息接收者的名称，来自 AS2 消息的标头。 （必需） |
| as2From | 字符串 | AS2 消息发送者的名称，来自 AS2 消息的标头。 （必需） |
| agreementName | 字符串 | 解析消息的 AS2 协议名称。 (可选) |
| direction | 字符串 | 消息流的方向，接收或发送。 （必需） |
| messageId | 字符串 | AS2 消息 ID，来自 AS2 消息的标头（可选） |
| dispositionType |字符串 | 邮件处置说明 (MDN) 处置类型值。 (可选) |
| fileName | 字符串 | 文件名，来自 AS2 消息的标头。 (可选) |
| isMessageFailed |布尔 | AS2 消息是否失败。 （必需） |
| isMessageSigned | 布尔 | AS2 消息是否已签名。 （必需） |
| isMessageEncrypted | 布尔 | AS2 消息是否已加密。 （必需） |
| isMessageCompressed |布尔 | AS2 消息是否已压缩。 （必需） |
| correlationMessageId | 字符串 | AS2 消息 ID，用于将消息与 MDN 相关联。 (可选) |
| incomingHeaders |JToken 字典 | 传入 AS2 消息标头的详细信息。 (可选) |
| outgoingHeaders |JToken 字典 | 传出 AS2 消息标头的详细信息。 (可选) |
| isNrrEnabled | 布尔 | 如果值未知，请使用默认值。 （必需） |
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

| properties | Type | 描述 |
| --- | --- | --- |
| senderPartnerName | 字符串 | AS2 消息发送者的合作伙伴名称。 (可选) |
| receiverPartnerName | 字符串 | AS2 消息接收者的合作伙伴名称。 (可选) |
| as2To | 字符串 | 接收 AS2 消息的合作伙伴名称。 （必需） |
| as2From | 字符串 | 发送 AS2 消息的合作伙伴名称。 （必需） |
| agreementName | 字符串 | 解析消息的 AS2 协议名称。 (可选) |
| direction |字符串 | 消息流的方向，接收或发送。 （必需） |
| messageId | 字符串 | AS2 消息 ID。 (可选) |
| originalMessageId |字符串 | AS2 原始消息 ID。 (可选) |
| dispositionType | 字符串 | MDN 处置类型值。 (可选) |
| isMessageFailed |布尔 | AS2 消息是否失败。 （必需） |
| isMessageSigned |布尔 | AS2 消息是否已签名。 （必需） |
| isNrrEnabled | 布尔 | 如果值未知，请使用默认值。 （必需） |
| statusCode | 枚举 | 允许的值为 **Accepted**、**Rejected** 和 **AcceptedWithErrors**。 （必需） |
| micVerificationStatus | 枚举 | 允许的值为 **NotApplicable**、**Succeeded** 和 **Failed**。 （必需） |
| correlationMessageId | 字符串 | 相关性 ID。 原始消息 ID（为其配置 MDN 的消息的消息 ID）。 (可选) |
| incomingHeaders | JToken 字典 | 指示传入消息标头的详细信息。 (可选) |
| outgoingHeaders |JToken 字典 | 指示传出消息标头的详细信息。 (可选) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 协议跟踪架构

有关 B2B 协议跟踪架构的信息，请参阅：

* [X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B 自定义跟踪架构](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

* 了解如何[监视 B2B 消息](logic-apps-monitor-b2b-message.md)
* 了解如何[在 Azure Monitor 日志中跟踪 B2B 消息](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)