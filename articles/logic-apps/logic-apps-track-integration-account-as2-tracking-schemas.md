---
title: B2B 消息的 AS2 跟踪架构
description: 创建跟踪架构以在 Azure 逻辑应用中监视 AS2 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906970"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>创建用于在 Azure 逻辑应用中跟踪 AS2 消息的架构

若要帮助你监视企业到企业 (B2B) 事务的成功、错误和消息属性，可以在集成帐户中使用以下 AS2 跟踪架构：

* AS2 消息跟踪架构
* AS2 消息处置通知 （MDN） 跟踪架构

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
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| properties | 必选 | 类型 | 说明 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | AS2 邮件发件人的合作伙伴名称 |
| receiverPartnerName | 否 | String | AS2 消息接收方的合作伙伴名称 |
| as2To | 是 | String | AS2 消息接收方的名称来自 AS2 消息的标头 |
| as2From | 是 | String | AS2 消息发件人的姓名来自 AS2 消息的标头 |
| agreementName | 否 | String | 解析消息的 AS2 协议名称 |
| direction | 是 | String | 消息流的方向，该方向为`receive`或`send` |
| messageId | 否 | String | AS2 消息头中的 AS2 消息 ID |
| dispositionType | 否 | String | 消息处置通知 （MDN） 处置类型值 |
| fileName | 否 | String | 来自 AS2 消息头的文件名 |
| isMessageFailed | 是 | Boolean | AS2 消息是否失败 |
| isMessageSigned | 是 | Boolean | AS2 消息是否已签名 |
| isMessageEncrypted | 是 | Boolean | AS2 消息是否已加密 |
| isMessageCompressed | 是 | Boolean | AS2 消息是否被压缩 |
| correlationMessageId | 否 | String | AS2 消息 ID，用于将消息与 MN 关联 |
| incomingHeaders | 否 | JToken 字典 | 传入 AS2 消息标头详细信息 |
| outgoingHeaders | 否 | JToken 字典 | 传出 AS2 消息头详细信息 |
| isNrrEnabled | 是 | Boolean | 如果值不知道，是否使用默认值 |
| isMdnExpected | 是 | Boolean | 如果值不知道，是否使用默认值 |
| mdnType | 是 | 枚举 | 允许的值： `NotConfigured` `Sync`、 和`Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN 跟踪架构

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

| properties | 必选 | 类型 | 说明 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | AS2 邮件发件人的合作伙伴名称 |
| receiverPartnerName | 否 | String | AS2 消息接收方的合作伙伴名称 |
| as2To | 是 | String | 接收 AS2 消息的合作伙伴名称 |
| as2From | 是 | String | 发送 AS2 消息的合作伙伴名称 |
| agreementName | 否 | String | 解析消息的 AS2 协议名称 |
| direction | 是 | String | 消息流的方向，该方向为`receive`或`send` |
| messageId | 否 | String | AS2 消息 ID |
| originalMessageId | 否 | String | AS2 原始消息 ID |
| dispositionType | 否 | String | MDN 处置类型值 |
| isMessageFailed | 是 | Boolean | AS2 消息是否失败 |
| isMessageSigned | 是 | Boolean | AS2 消息是否已签名 |
| isNrrEnabled | 是 | Boolean | 如果值不知道，是否使用默认值 |
| statusCode | 是 | 枚举 | 允许的值： `Accepted` `Rejected`、 和`AcceptedWithErrors` |
| micVerificationStatus | 是 | 枚举 | 允许的值：`NotApplicable` `Succeeded`、 和`Failed` |
| correlationMessageId | 否 | String | 关联 ID，它是配置 MDN 的原始消息的 ID |
| incomingHeaders | 否 | JToken 字典 | 传入消息标头详细信息 |
| outgoingHeaders | 否 | JToken 字典 | 传出消息头详细信息 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 协议跟踪架构

有关 B2B 协议跟踪架构的信息，请参阅：

* [X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B 自定义跟踪架构](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 日志监视 B2B 消息](../logic-apps/monitor-b2b-messages-log-analytics.md)