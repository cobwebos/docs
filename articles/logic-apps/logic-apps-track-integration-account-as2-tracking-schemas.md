---
title: B2B 消息的 AS2 跟踪架构
description: 在 Azure 逻辑应用中创建跟踪架构来监视 AS2 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906970"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>在 Azure 逻辑应用中创建用于跟踪 AS2 消息的架构

为了帮助你监视企业到企业（B2B）事务的成功、错误和消息属性，你可以在集成帐户中使用以下 AS2 跟踪架构：

* AS2 消息跟踪架构
* AS2 消息处置通知（MDN）跟踪架构

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

| properties | 必选 | 键入 | description |
|----------|----------|------|-------------|
| senderPartnerName | No | String | AS2 消息发送方的伙伴名称 |
| receiverPartnerName | No | String | AS2 消息接收方的伙伴名称 |
| as2To | 用户帐户控制 | String | As2 消息接收方的名称（来自 AS2 消息的标头） |
| as2From | 用户帐户控制 | String | As2 消息发送方的名称（来自 AS2 消息的标头） |
| agreementName | No | String | 解析消息的 AS2 协议的名称 |
| 方向键 | 用户帐户控制 | String | 消息流的方向，该方向是 `receive` 或 `send` |
| messageId | No | String | AS2 消息标头中的 AS2 消息 ID |
| dispositionType | No | String | 消息处置通知（MDN）处理类型值 |
| fileName | No | String | AS2 消息标头中的文件名 |
| isMessageFailed | 用户帐户控制 | Boolean | AS2 消息是否失败 |
| isMessageSigned | 用户帐户控制 | Boolean | AS2 消息是否已签名 |
| isMessageEncrypted | 用户帐户控制 | Boolean | AS2 消息是否已加密 |
| isMessageCompressed | 用户帐户控制 | Boolean | AS2 消息是否已压缩 |
| correlationMessageId | No | String | AS2 消息 ID，用于将消息与 Mdn 关联 |
| incomingHeaders | No | JToken 字典 | 传入 AS2 消息标头详细信息 |
| outgoingHeaders | No | JToken 字典 | 传出 AS2 消息标头详细信息 |
| isNrrEnabled | 用户帐户控制 | Boolean | 值未知时是否使用默认值 |
| isMdnExpected | 用户帐户控制 | Boolean | 如果值未知，是否使用默认值 |
| mdnType | 用户帐户控制 | Enum | 允许的值： `NotConfigured`、`Sync`和 `Async` |
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

| properties | 必选 | 键入 | description |
|----------|----------|------|-------------|
| senderPartnerName | No | String | AS2 消息发送方的伙伴名称 |
| receiverPartnerName | No | String | AS2 消息接收方的伙伴名称 |
| as2To | 用户帐户控制 | String | 接收 AS2 消息的合作伙伴名称 |
| as2From | 用户帐户控制 | String | 发送 AS2 消息的合作伙伴名称 |
| agreementName | No | String | 解析消息的 AS2 协议的名称 |
| 方向键 | 用户帐户控制 | String | 消息流的方向，该方向是 `receive` 或 `send` |
| messageId | No | String | AS2 消息 ID |
| originalMessageId | No | String | AS2 原始消息 ID |
| dispositionType | No | String | MDN 处置类型值 |
| isMessageFailed | 用户帐户控制 | Boolean | AS2 消息是否失败 |
| isMessageSigned | 用户帐户控制 | Boolean | AS2 消息是否已签名 |
| isNrrEnabled | 用户帐户控制 | Boolean | 如果值未知，是否使用默认值 |
| statusCode | 用户帐户控制 | Enum | 允许的值： `Accepted`、`Rejected`和 `AcceptedWithErrors` |
| micVerificationStatus | 用户帐户控制 | Enum | 允许的值：`NotApplicable`、`Succeeded`和 `Failed` |
| correlationMessageId | No | String | 相关 ID，它是配置了 MDN 的原始消息的 ID |
| incomingHeaders | No | JToken 字典 | 传入消息标头详细信息 |
| outgoingHeaders | No | JToken 字典 | 传出消息标头详细信息 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 协议跟踪架构

有关 B2B 协议跟踪架构的信息，请参阅：

* [X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B 自定义跟踪架构](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

* [利用 Azure Monitor 日志监视 B2B 消息](../logic-apps/monitor-b2b-messages-log-analytics.md)